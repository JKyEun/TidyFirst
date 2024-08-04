# IndexedDB

## **로컬 스토리지의 문제점**

### **저장 용량 제한**

-   로컬 스토리지는 일반적으로 5MB 정도의 저장 용량을 제공합니다. 대용량 데이터를 저장하기에는 부족합니다.

### **동기적 API**

-   로컬 스토리지는 동기적 API를 사용합니다. 이는 데이터 저장 및 검색 작업이 완료될 때까지 브라우저의 다른 작업이 블로킹된다는 것을 의미합니다. 특히 대용량 데이터를 처리할 때 성능 문제가 발생할 수 있습니다.

### **단순한 데이터 구조**

-   로컬 스토리지는 문자열 형태의 키-값 쌍만 저장할 수 있습니다. 복잡한 데이터 구조를 저장하고 관리하기 어렵습니다.
-   이 때문에 객체 형태를 저장하려면 JSON 형태로 변환하는 작업을 거치면서 에러가 날 확률이 높아집니다.

### **보안 문제**

-   로컬 스토리지에 저장된 데이터는 클라이언트 측에서 쉽게 접근할 수 있어 보안에 취약할 수 있습니다.

### 속도

-   느린 속도 때문에 빈번한 트랜잭션이 필요한 경우 유저 경험을 하락시킬 수 있습니다.

## **IndexedDB를 사용하여 문제 해결**

### **대용량 데이터 저장**

-   IndexedDB는 로컬 스토리지보다 훨씬 큰 용량을 제공합니다. 수백 MB 이상의 데이터를 저장할 수 있어 대용량 데이터 저장에 적합합니다.

### **비동기적 API**

-   IndexedDB는 비동기적 API를 사용합니다. 데이터 저장 및 검색 작업이 비동기적으로 이루어지므로, 브라우저의 다른 작업이 블로킹되지 않습니다. 이는 성능 향상에 도움이 됩니다.

### **구조화된 데이터 저장**

-   IndexedDB는 객체 저장소를 사용하여 구조화된 데이터를 저장할 수 있습니다. JSON 객체나 복잡한 데이터 구조를 효율적으로 저장하고 관리할 수 있습니다.

### **복잡한 쿼리 지원**

-   IndexedDB는 인덱스를 사용하여 복잡한 쿼리를 수행할 수 있습니다. 특정 필드에 대한 검색이나 정렬을 효율적으로 수행할 수 있습니다.

### **트랜잭션 지원**

-   IndexedDB는 트랜잭션을 통해 데이터 일관성을 유지할 수 있습니다. 여러 작업을 하나의 트랜잭션으로 묶어 원자성을 보장할 수 있습니다.

### 보안

-   로컬 스토리지와 달리 대부분의 브라우저는 보안이 강화된 환경(HTTPS)에서만 IndexedDB를 사용할 수 있도록 요구합니다.
-   로컬 스토리지와 달리 사용자가 명시적으로 동의한 경우에만 데이터를 저장할 수 있습니다.

## IndexedDB의 사용

TODO List의 CRUD 예시

### IndexedDB 세팅

```
  useEffect(() => {
    const dbName = 'TodoDB';
    const storeName = 'todos';

    const request = indexedDB.open(dbName, 1);

    request.onupgradeneeded = (event) => {
      const db = (event.target as IDBOpenDBRequest).result;
      if (!db.objectStoreNames.contains(storeName)) {
        db.createObjectStore(storeName, { keyPath: 'id', autoIncrement: true });
      }
    };

    request.onsuccess = (event) => {
      const db = (event.target as IDBOpenDBRequest).result;
      setDb(db);
      fetchTodos(db);
    };

    request.onerror = (event) => {
      console.error('Error opening IndexedDB:', (event.target as IDBRequest).error);
    };
  }, []);
```

### CRUD

```tsx
const fetchTodos = (db: IDBDatabase) => {
    const transaction = db.transaction(['todos'], 'readonly');
    const store = transaction.objectStore('todos');
    const request = store.getAll();

    request.onsuccess = () => {
        setTodos(request.result);
    };
};

const addTodo = (task: string) => {
    if (!db) return;
    const transaction = db.transaction(['todos'], 'readwrite');
    const store = transaction.objectStore('todos');
    store.add({ task });

    transaction.oncomplete = () => {
        fetchTodos(db);
    };
};

const updateTodo = (id: number, task: string) => {
    if (!db) return;
    const transaction = db.transaction(['todos'], 'readwrite');
    const store = transaction.objectStore('todos');
    store.put({ id, task });

    transaction.oncomplete = () => {
        fetchTodos(db);
    };
};

const deleteTodo = (id: number) => {
    if (!db) return;
    const transaction = db.transaction(['todos'], 'readwrite');
    const store = transaction.objectStore('todos');
    store.delete(id);

    transaction.oncomplete = () => {
        fetchTodos(db);
    };
};
```

### View

```tsx
<div>
    <h1>Todo List</h1>
    <input type='text' value={newTask} onChange={e => setNewTask(e.target.value)} />
    <button onClick={() => addTodo(newTask)}>Add Todo</button>
    <ul>
        {todos.map(todo => (
            <li key={todo.id}>
                <input type='text' value={todo.task} onChange={e => updateTodo(todo.id, e.target.value)} />
                <button onClick={() => deleteTodo(todo.id)}>Delete</button>
            </li>
        ))}
    </ul>
</div>
```
