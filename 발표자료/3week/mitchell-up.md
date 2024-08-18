# Websocket과 React-Query를 통합하여 서버상태 실시간 업데이트하기

OO기업의 과제를 진행하여 적용해봤던 내용을 공유합니다. 주식과 관련한 기능을 만드는 과제이며, 웹소켓을 사용하여 실시간 데이터를 다루는 것이 주요한 지점이였습니다.

<br/>

## 개요

최초 데이터 로드는 Rest API를 통해서 진행하며, 업데이트 되는 내용은 Websocket을 통해서 서버를 통해 전달받는 형태였습니다.

1. TanStack Query: REST API를 이용하여 초기 데이터를 불러오고 캐시합니다.
2. WebSocket: 실시간으로 전달되는 메시지를 전달받습니다.
3. queryClient: `queryClient.setQueryData`를 이용하여 전달된 메시지를 실시간으로 업데이트 합니다.

<br/>

## 예시코드: 환율정보

### 초기로딩을 위한 쿼리

REST API를 호출할 준비를 하고, react-query의 훅을 사용하여 초기 환율정보를 가져오고 캐싱할 수 있도록 합니다.

```ts
// 서버로부터 최초 환율 정보를 가져옵니다.
async function getExchangeRate() {
  return http.get<ExchangeRate>("/rate").then((res) => res.data);
}

// useSuspenseQuery에 적용할 queryOptions입니다.
export const getExchangeRateQueryOptions = queryOptions({
  queryKey: queryKeys.info.rate,
  queryFn: getExchangeRate,
});

// 환율정보를 가져오고, 실시간으로 업데이트하는 커스텀 훅입니다.
export function useGetExchangeRate() {
  const { data: exchangeRate } = useSuspenseQuery({
    ...getExchangeRateQueryOptions,
  });

  return {
    exchangeRate,
  };
}
```

### 실시간 업데이트 적용하기

Websocket으로부터 실시간 데이터를 전달받아 환율정보를 실시간으로 업데이트하도록 합니다. 이 때 `queryClient`를 사용하여 캐싱되어 있던 데이터를 업데이트 하도록 합니다.

```ts
// 서버로부터 최초 환율 정보를 가져옵니다.
async function getExchangeRate() {
  return http.get<ExchangeRate>("/rate").then((res) => res.data);
}

// useSuspenseQuery에 적용할 queryOptions입니다.
export const getExchangeRateQueryOptions = queryOptions({
  queryKey: queryKeys.info.rate,
  queryFn: getExchangeRate,
});

// 환율정보를 가져오고, 실시간으로 업데이트하는 커스텀 훅입니다.
export function useGetExchangeRate() {
  const { socket } = useStockSocketContext(); // 컨텍스트 안에서 싱글턴으로 관리되도록 구현되어 있습니다.
  const queryClient = useQueryClient();

  const { data: exchangeRate } = useSuspenseQuery({
    ...getExchangeRateQueryOptions,
  });

  useEffect(() => {
    // 환율정보를 구독합니다. 구독하고 나면
    // `socket`으로 환율정보 메시지를 지속적으로 받게 됩니다.
    const subId = socket.send({
      type: "RATE",
      command: "SUBSCRIBE",
    });

    return () => {
      socket.send({
        guid: subId,
        command: "UNSUBSCRIBE",
      });
    };
  }, [socket]);

  useEffect(() => {
    // 내부적으로 구독 타입에 따라 적절한 핸들러를 등록하여 실행할 수 있도록 구현되어 있습니다.
    // 핸들러는 환율정보를 담고 있는 `message`를 인자로 가지고 있습니다.
    // 새로운 환율정보를 받을 때 마다 `queryClient.setQueryData`를 통해서
    // 새로운 환율정보로 업데이트하도록 처리합니다.
    const handlerId = socket.addReceiveHandler("RATE", (message) => {
      queryClient.setQueryData(
        getExchangeRateQueryOptions.queryKey,
        message.rate
      );
    });

    return () => {
      socket.removeReceiveHandler("RATE", handlerId);
    };
  }, [queryClient, socket]);

  return {
    exchangeRate,
  };
}
```

<br/>

## 그 외

```ts
const { socket } = useStockSocketContext();
```

내부적으로 정의된 프로토콜에 따라 타입안정적이고, 효율적으로 실시간 데이터를 처리할 수 있도록 구성되어 있습니다. Websocket client를 Context로 관리하여 실시간 데이터가 필요한 컴포넌트에서 효율적으로 접근 할 수 있습니다.

```ts
type StockSocketContextType = {
  socket?: StockWebSocket;
};

const SocketContext = createContext<StockSocketContextType | null>(null);

const SOCKET_URL = process.env.REACT_APP_API_SOCKET_URL;

export function StockSocketProvider({ children }: { children: ReactNode }) {
  const [socket] = useState<StockWebSocket>(
    // 컨텍스트를 통해 websocket client에 접근할 수 있도록 하여
    // 불필요한 서버와의 연결을 줄일 수 있습니다. (컨택스트 내 싱글턴으로 유지)
    () => new StockWebSocket(SOCKET_URL)
  );

  useEffect(() => {
    socket.onConnect((event) => {
      console.log("연결됨", event);
    });

    socket.onReceive();

    socket.onDisconnect((event) => {
      console.log("연결끊김", event);
    });

    return () => {
      socket.disconnect();
    };
  }, [socket]);

  return (
    <SocketContext.Provider
      value={{
        socket,
      }}
    >
      {children}
    </SocketContext.Provider>
  );
}

export function useStockSocketContext() {
  const socketContext = useContext(SocketContext);

  assert(socketContext?.socket != null, "Socket context should be initialized");

  return {
    socket: socketContext.socket,
  };
}
```

`StockWebSocket`은 `WebSocketClient`라는 `WebSocket` 헬퍼클래스 구현하여 이를 확장한 클래스입니다. 주식가격 등 주식과 관련하여 특화되어 있습니다.
