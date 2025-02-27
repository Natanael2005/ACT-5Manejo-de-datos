# Actividad 05 ➖ Manejo de datos descripcion 


En esta actividad se implementaron 2 ejemplos de manejo de datos en una App de Expo cuales son los siguientes **SecureStore** y **SQLite**

1. Vamos a comenzar con **SecureStore**
    - Lo primero que se realizó fue instalar la dependencia de SecureStore:
      ```bash
      npx expo install expo-secure-store
      ```
    - Luego, importamos SecureStore en nuestro archivo:
      ```jsx
      import * as SecureStore from "expo-secure-store";
      ```
      
    - Hemos creado tres funciones principales para gestionar los valores almacenados de manera segura. \
      \
      🟣 **Crear un valor**
      ```jsx
      async function save(key, value) {
      await SecureStore.setItemAsync(key, value);
      }
      ```
      
      🟣 **Cargar un valor**
      ```jsx
      async function getValueFor(key) {
      let result = await SecureStore.getItemAsync(key);
      if (result) {
      alert("El valor es:\n" + result);
      } else {
      alert("No hay valores almacenados bajo esa clave");
      }
      }
      ```
      
       🟣 **Eliminar un valor**
      ```jsx
      async function deleteValueFor(key) {
      try {
      await SecureStore.deleteItemAsync(key);
      alert("Valor eliminado.");
      } catch (error) {
      alert("Error: " + error.message);
      }
      }
      ```
      
      -Este valor es agregado a la clave proporcionada por el usuario y puede ser recuperado o eliminado según la necesidad.
      ```jsx
        const [key, onChangeKey] = useState("Llave");
        const [value, onChangeValue] = useState("Valor");
        const [getKey, onChangeGetKey] = useState("");
      ```
    - Ahora, llamamos a las funciones para que podamos usarlas:
      ```jsx
      return (
      <View style={styles.container}>
      <Text style={styles.paragraph}>SecureStore</Text>
      <Text style={styles.paragraph}>Ingrese la clave y el valor que desea almacenar</Text>

      <TextInput
      style={styles.textInput}
      clearTextOnFocus
      onChangeText={(text) => onChangeKey(text)}
      value={key}
      />
      <TextInput
      style={styles.textInput}
      clearTextOnFocus
      onChangeText={(text) => onChangeValue(text)}
      value={value}
      />

      <Button
      title="Guardar"
      onPress={() => {
      save(key, value);
      onChangeKey("Llave");
      onChangeValue("Valor");
      }}
      />

      <Text style={styles.paragraph}>Ingrese la clave para el valor que desea eliminar</Text>
      <Button
      title="Eliminar"
      onPress={() => {
        deleteValueFor(key);
        onChangeKey("");
      }}
      />

      <Text style={styles.paragraph}>Ingrese la clave para obtener el valor</Text>
      <TextInput
      style={styles.textInput}
      onChangeText={(text) => onChangeGetKey(text)}
      value={getKey}
      placeholder="Ingrese la clave para el valor que desea obtener"
      />
      <Button
      title="Obtener"
      onPress={() => {
        getValueFor(getKey);
        onChangeGetKey("");
      }}
      />
      </View>
      );
      ```
    - Y con esto finaliza la parte de **SecureStore**
  
2. Comenzamos con la parte de **SQLite**
    - Lo primero que hay que hacer es instalar la dependencia de SQLite
      ```bash      
      npx expo install expo-sqlite
      ```
    - Seguido de lo anterior, importamos a nuestro archivo sqlite.jsx lo siguiente
      ```jsx
      import { SQLiteProvider, useSQLiteContext } from 'expo-sqlite';
      ```
    - Ahora lo que hacemos es crear la base de datos `test.db`
      ```jsx
      export default function App() {
        return (
          <View style={styles.container}>
            <SQLiteProvider databaseName="test.db" onInit={migrateDbIfNeeded}>
              <Header />
              <Content />
            </SQLiteProvider>
          </View>
        );
      }      
      ```
    - Ahora creamos nuestra tabla e insertamos nustros datos
      ```jsx
      async function migrateDbIfNeeded(db) {
        const DATABASE_VERSION = 1;
        let { user_version: currentDbVersion } = await db.getFirstAsync('PRAGMA user_version');
        if (currentDbVersion >= DATABASE_VERSION) {
          return;
        }
        if (currentDbVersion === 0) {
          await db.execAsync(`
            PRAGMA journal_mode = 'wal';
            CREATE TABLE todos (id INTEGER PRIMARY KEY NOT NULL, value TEXT NOT NULL, intValue INTEGER);
          `);
          await db.runAsync('INSERT INTO todos (value, intValue) VALUES (?, ?)', 'hello', 1);
          await db.runAsync('INSERT INTO todos (value, intValue) VALUES (?, ?)', 'world', 2);
          currentDbVersion = 1;
        }
        await db.execAsync(`PRAGMA user_version = ${DATABASE_VERSION}`);
      }
      ```
    - Realizamos un Select a nuestra tabla para guardar los datos
      ```jsx
      export function Content() {
        const db = useSQLiteContext();
        const [todos, setTodos] = useState([]);
      
        useEffect(() => {
          async function setup() {
            const result = await db.getAllAsync('SELECT * FROM todos');
            setTodos(result);
          }
          setup();
        }, []);
      ```
    - Y ultimo mostramos los datos en pantalla
      ```jsx
        return (
          <View style={styles.contentContainer}>
            {todos.map((todo, index) => (
              <View style={styles.todoItemContainer} key={index}>
                <Text>{todo.intValue} - {todo.value}</Text>
              </View>
            ))}
          </View>
        );
      }
      ```

      ## Chan Uc Felipe Natanael SM-54
      






      
