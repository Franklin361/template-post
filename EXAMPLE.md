# ¿Cómo crear un buscador con efecto "debounce"? 🔎

Tabla de contenido
> 📌 [Introducción.](#1)  
> 📌 [Tecnologías a utilizar.](#2)   
> 📌 [¿Qué es el efecto "Debounce"?](#3)  
> 📌 [Creando el proyecto.](#4)   
> 📌 [Primeros pasos.](#5)   
> 📌 [Creando el input.](#6)   
> 📌 [Manejando el estado del input.](#7)   
> 📌 [Creando la función para la petición a la API.](#8)   
> 📌 [Creando el efecto Debounce.](#9)   
> 📌 [Haciendo la llamada a la API.](#10)   
> 📌 [Creando el componente Pokemon.tsx.](#11)   
> 📌 [Usando nuestro componente Pokemon.](#12)   
> 📌 [Limpiando la lógica de nuestro componente.](#13)   
>> 📌 [1. Manejando la lógica para controlar el input.](#14)   
>> 📌 [2. Manejando la lógica para la llamada a la API.](#15)   
>> 📌 [3. Manejando la lógica para el efecto Debounce.](#16)      
>
> 📌 [Conclusión.](#17)   
>> 📌 [Código fuente.](#18)   

&nbsp;

## 🎈 Introducción <a id="1"></a>

El propósito de este post es enseñar una manera sencilla de como realizar un pequeño buscador con un efecto debounce. 
Dicho proyecto puede extenderse de muchas maneras, pero tratare de hacerlo algo básico pero eficiente.

> 🚨 Nota: Este post requiere que sepas las bases de **React con TypeScript (hooks básicos y peticiones con fetch)**.
>

Cualquier tipo de Feedback es bienvenido, gracias y espero disfrutes el articulo.🤗

&nbsp;

## 🎈 Tecnologías a utilizar. <a id="2"></a>

- ▶️ React JS (version 18)
- ▶️ Vite JS
- ▶️ TypeScript
- ▶️ [Pokemon API](https://pokeapi.co)
- ▶️ CSS vanilla (Los estilos los encuentras en el repositorio al final de este post) 

&nbsp;

## 🎈 ¿Qué es el efecto "Debounce"? <a id="3"></a>

El efecto rebote (**debounce**) es cuando no se ejecutan al momento de su invocación. En lugar de eso, su ejecución es retrasada por un periodo predeterminado de tiempo. Si la misma función es invocada de nuevo, la ejecución previa es cancelada y el tiempo de espera se reinicia.

&nbsp;

## 🎈 Creando el proyecto. <a id="4"></a>

Al proyecto le colocaremos el nombre de: **`search-debounce`** (opcional, tu le puedes poner el nombre que gustes).
```bash
npm init vite@latest
```
Creamos el proyecto con Vite JS y seleccionamos React con TypeScript.  
Luego ejecutamos el siguiente comando para navegar al directorio que se acaba de crear.

```bash
cd search-debounce
```
Luego instalamos las dependencias.
```bash
npm install
```
Después abrimos el proyecto en un editor de código (en mi caso VS code).
```bash
code .
```

&nbsp;

## 🎈 Primeros pasos. <a id="5"></a>

Dentro de la carpeta **`src/App.tsx`** borramos todo el contenido del archivo y colocamos un componente funcional que muestre un titulo.
```javascript
const App = () => {
  return (
    <div className="container">
      <h1> <span>Search Engine</span> whit <span>Debounce Effect</span> </h1>
    </div>
  )
}
export default App
```

Debería de verse así 👀:

![Title](https://res.cloudinary.com/dnxchppfm/image/upload/v1655994217/posts/search-debounce/01_sp9sa9.png)

&nbsp;

## 🎈 Creando el input. <a id="6"></a>

Ahora creamos la carpeta **`src/components`** y dentro de la carpeta creamos el archivo **`Input.tsx`** y dentro agregamos lo siguiente: 

```jsx
export const Input = () => {
  return (
    <>
        <label htmlFor="pokemon">Name or ID of a Pokemon</label>
        <input type="text" id="pokemon" placeholder="Example: Pikachu" />
    </>
  )
}
```
Una vez hecho, lo importamos en el archivo **`App.tsx`**

```jsx
import { Input } from "./components/Input"

const App = () => {

  return (
    <div className="container">
      <h1> <span>Search Engine</span> whit <span>Debounce Effect</span> </h1>

      <Input/>
    </div>
  )
}
export default App
```

Debería de verse así 👀:

![Input](https://res.cloudinary.com/dnxchppfm/image/upload/v1655994217/posts/search-debounce/02_avk6ld.png)

&nbsp;

## 🎈 Manejando el estado del input. <a id="7"></a>

> 🚨 Nota: Esta **NO** es la única forma para realizar este ejercicio, solo es una opción! Si tienes una mejor manera, me gustaría que la compartieras por los comentarios por favor. 😌
>

En este caso voy a manejar el estado del input en un nivel superior, o sea, el componente App del archivo **`App.tsx`**

Esto lo haremos, debido a que necesitamos el valor del input disponible en **`App.tsx`**, ya que ahi se hará la petición a la API y el efecto debounce.

1 - Primero creamos el estado para manejar el valor del input. 
```js
const [value, setValue] = useState('');
```
2 - Creamos una función para que actualice el estado del input cuando el input haga un cambio.  
Esta función recibe como parámetro el evento que emite el input, de dicho evento obtendremos la propiedad **target** y luego la propiedad **value**, la cual es la que mandaremos a nuestro estado.
```js
const onChange = (e: React.ChangeEvent<HTMLInputElement>) => setValue(e.target.value); 
```
3 - Por consiguiente, toca mandar la función y el valor del estado al input.
```jsx
const App = () => {

  return (
    <div className="container">
      <h1> <span>Search Engine</span> whit <span>Debounce Effect</span> </h1>

      <Input {...{value, onChange}}/>
    </div>
  )
}
export default App
```
4 - En el componente Input agregamos una interfaz para recibir las propiedades por parámetro en el archivo **`Input.tsx`**.
```js
interface Props {
   value: string;
   onChange: (e: React.ChangeEvent<HTMLInputElement>) => void;
}
```
5 - Desestructuramos las propiedades y las agregamos al input.  
La función onChange, la colocamos en la propiedad **onChange** del input y lo mismo con el la  value propiedad **value**.
```ts
interface Props {
   value: string;
   onChange: (e: React.ChangeEvent<HTMLInputElement>) => void;
}

export const Form = ({ onChange, value }:Props) => {

  return (
    <>
        <label htmlFor="pokemon">Name of a Pokemon</label>
        <input 
          type="text" 
          id="pokemon" 
          placeholder="Example: Pikachu" 
          value={value}
          onChange={onChange}
        />
    </>
  )
}
```
Y así ya tenemos controlado el estado de nuestro input. 🥳

&nbsp;

## 🎈 Creando la función para la petición a la API. <a id="8"></a>
Ahora creamos la carpeta **`src/utils`** y dentro colocamos un archivo llamado **`searchPokemon.ts`** y agregamos la siguiente función para hacer la petición, y buscar un pokemon por su nombre o ID.

> 🚨 Nota: La respuesta de la API tiene más propiedades de las que se representa en la interfaz **ResponseAPI**
>

Esta función recibe dos parámetros: 
- **pokemon**: es el nombre o ID del pokemon.
- **signal**: permite establecer escuchadores de eventos. En otras palabras, nos ayudara a cancelar la petición HTTP cuando el componente se desmonte o haga un cambio en el estado.  

Esta función retorna la data del pokemon si todo sale bien o null si algo sale mal.

```ts
export interface ResponseAPI {
    name: string;
    sprites: { front_default: string }
}

export const searchPokemon = async (pokemon: string, signal?: AbortSignal): Promise<ResponseAPI | null> => {
    try {
        
        const url = `https://pokeapi.co/api/v2/pokemon/${pokemon.toLowerCase().trim()}`
        const res = await fetch(url, { signal });
        
        if(res.status === 404) return null

        const data: ResponseAPI = await res.json();
        return data

    } catch (error) {
        console.log((error as Error).message);
        return null
    }
}
```

&nbsp;

## 🎈 Creando el efecto Debounce. <a id="9"></a>
En el archivo **`App.tsx`** creamos un estado, que nos servirá para guardar el valor del input.

```jsx
const [debouncedValue, setDebouncedValue] = useState();
```
Como estado inicial le mandamos el valor del estado del input (**value**).
```jsx
const [value, setValue] = useState('');

const onChange = (e: React.ChangeEvent<HTMLInputElement>) => setValue(e.target.value);

const [debouncedValue, setDebouncedValue] = useState(value);
```
Ahora, creamos un efecto para que cuando el valor del input cambie, ejecutamos la función **setTimeout** que actualizara el estado del **debouncedValue** enviando el nuevo valor del input, después de 1 segundo, y asi obtendremos la palabra clave o sea el pokemon, para hacer la petición a la API.

Al final del efecto, ejecutamos el método de limpieza, que consiste en limpiar la función **setTimeout**, es por eso que la guardamos en una constante llamada **`timer`**

```js
useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), 1000)

    return () => clearTimeout(timer)
}, [value]);
```
Entonces por el momento nuestro archivo App.tsx quedaría de la siguiente manera: 
```jsx
import { useEffect, useState } from 'react';
import { Input } from "./components/Input"

const App = () => {

  const [value, setValue] = useState('');
  const onChange = (e: React.ChangeEvent<HTMLInputElement>) => setValue(e.target.value);

  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {

    const timer = setTimeout(() => setDebouncedValue(value), delay || 500)

    return () => clearTimeout(timer)
  }, [value, delay]);

  return (
    <div className="container">
      <h1> <span>Search Engine</span> whit <span>Debounce Effect</span> </h1>

      <Input {...{ value, onChange }} />
    </div>
  )
}
export default App
```

&nbsp;

## 🎈 Haciendo la llamada a la API. <a id="10"></a>
Una vez que tenemos el valor del input ya con el efecto debounce, toca hacer la llamada a la API.  
Para eso usaremos al función que creamos con anterioridad, **`searchPokemon.tsx`**.

Para ello, vamos a usar un efecto.
Primero creamos el **`controller`** el cual es el que nos ayudara a cancelar la petición HTTP, como mencionamos antes
Dentro del controller tenemos dos propiedades que nos interesan: 
- **abort()**: al ejecutarse, cancela la petición.
- **signal**: mantiene la conexión entre el controller y el la petición para saber cual se debe cancelar.

El **abort()** lo ejecutamos al final, al momento de que se desmonte el componente.

```jsx
useEffect(() => {
    
    const controller = new AbortController();
    
    return () => controller.abort();

  }, []);
```
La dependencia de este efecto sera el valor del debouncedValue, ya que cada vez que cambie este valor, debemos hacer una nueva petición para buscar al nuevo pokemon.
```js
useEffect(() => {
    const controller = new AbortController();

    return () => controller.abort();

  }, [debouncedValue])
```
Hacemos una condición, en la que solo si el existe el valor de **debouncedValue** y tiene alguna palabra o numero, haremos la petición.
```js
useEffect(() => {
    const controller = new AbortController();
    
    if (debouncedValue) {

    }

    return () => controller.abort();
  }, [debouncedValue])
```

Dentro del if llamamos la función de **searchPokemon** y le mandamos el valor de **debouncedValue** y también la propiedad **signal** del **controller**

```js
useEffect(() => {
    const controller = new AbortController();

    if (debouncedValue) {
        searchPokemon(debouncedValue, controller.signal)
    }

    return () => controller.abort();
  }, [debouncedValue])
```
Y como la función **searchPokemon** regresa una promesa y dentro del efecto no es permitido usar **async/await**, usaremos **.then** para resolver la promesa y obtener el valor que retorna.
```js
useEffect(() => {
    const controller = new AbortController();

    if (debouncedValue) {
        searchPokemon(debouncedValue, controller.signal)
            .then(data => {
            console.log(data) //pokemon | null
        })
    }

    return () => controller.abort();
  }, [debouncedValue])
```
Al final debería de verse asi. 👀
```js
import { useEffect, useState } from 'react';
import { Input } from "./components/Input"
import { searchPokemon } from "./utils/searchPokemon";

const App = () => {

  const [value, setValue] = useState('');
  const onChange = (e: React.ChangeEvent<HTMLInputElement>) => setValue(e.target.value);

  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {

    const timer = setTimeout(() => setDebouncedValue(value), delay || 500)

    return () => clearTimeout(timer)
  }, [value, delay]);


  useEffect(() => {

    const controller = new AbortController();

    if (debouncedValue) {
      searchPokemon(debouncedValue, controller.signal)
        .then(data => {
            console.log(data) //pokemon | null
        })
    }

    return () => controller.abort();

  }, [debouncedValue])


  return (
    <div className="container">
      <h1> <span>Search Engine</span> whit <span>Debounce Effect</span> </h1>
      <Input {...{ value, onChange }} />
      
    </div>
  )
}
export default App
```

&nbsp;

## 🎈 Creando el componente Pokemon.tsx. <a id="11"></a>

1 - Primero creamos el componente funcional vació.

```js
export const Pokemon = () => {
  return (
    <></>
  )
}
```
2 - Agregamos la interfaz de **`ResponseAPI`** ya que vamos a recibir por props el pokemon, el cual puede contener la data del pokemon o un valor nulo.
```js
import { ResponseAPI } from "../utils/searchPokemon"

export const Pokemon = ({ pokemon }: { pokemon: ResponseAPI | null }) => {
  
  return (
    <></>
  )
}
```
3 - Hacemos una evaluación donde:
- Si el la propiedad pokemon es nula, mostramos el mensaje de "No results".
- Si la propiedad pokemon contiene la data del pokemon, mostramos su nombre y una imagen 
```js
import { ResponseAPI } from "../utils/searchPokemon"

export const Pokemon = ({ pokemon }: { pokemon: ResponseAPI | null }) => {
  
  return (
    <>
      {
        !pokemon
          ? <span>No results</span>
          : <div>
            <h3>{pokemon.name}</h3>
            <img src={pokemon.sprites.front_default} alt={pokemon.name} />
          </div>
      }
    </>
  )
}
```

Debería de verse asi si esta cargando 👀:
![Loading](https://res.cloudinary.com/dnxchppfm/image/upload/v1655994217/posts/search-debounce/03_cugi7i.png)

Debería de verse asi cuando no hay resultados 👀:
![No results](https://res.cloudinary.com/dnxchppfm/image/upload/v1655994217/posts/search-debounce/05_olggt6.png)

Debería de verse asi hay un pokemon 👀:
![Pokemon](https://res.cloudinary.com/dnxchppfm/image/upload/v1655994217/posts/search-debounce/04_zomwzc.png)


4 - Y ahora por ultimo, agregamos una ultima condición, en donde evaluamos si el pokemon existe (o sea que no es nula) y si es un objeto vació retornamos un fragmento.

>Esto es así ya que el estado inicial para almacenar a los pokemon sera un objeto vacío "**{}**".  
>Si no colocaremos esa condición, entonces la inicio de nuestra app, incluso sin haber tecleado nada en el input, ya nos aparecería el mensaje de "No results", y la idea es que aparezca después de que hayamos tecleado algo en el input y se haya hecho la llamada a la API.
>

```js
import { ResponseAPI } from "../utils/searchPokemon"

export const Pokemon = ({ pokemon }: { pokemon: ResponseAPI | null }) => {
  
  if(pokemon && Object.keys(pokemon).length === 0) return <></>;
  
  return (
    <>
      {
        !pokemon
          ? <span>No results</span>
          : <div>
            <h3>{pokemon.name}</h3>
            <img src={pokemon.sprites.front_default} alt={pokemon.name} />
          </div>
      }
    </>
  )
}
```

Asi quedaría nuestro componente pokemon, es hora de usarlo. 😌

&nbsp;

## 🎈 Usando nuestro componente Pokemon. <a id="12"></a>
En el archivo App.tsx agregaremos 2 nuevos estados:
- Para almacenar el pokemon encontrado, que tendrá un valor inicial de un objeto vacío.
- Para manejar un loading en lo que se hace la llamada a la API, que tendrá un valor inicial de falso.

```js
const [pokemon, setPokemon] = useState<ResponseAPI | null>({} as ResponseAPI);
const [isLoading, setIsLoading] = useState(false)
```
Ahora dentro del efecto donde hacemos la llamada a la API mediante la función **`searchPokemon`**, antes de hacer la llamada enviamos el valor de true al **setIsLoading** para activar el loading.  
Después, una vez obtenida la data dentro del **.then** le enviamos dicha data al **setPokemon** (el cual puede ser el pokemon o un valor nulo).
Y finalmente enviamos el valor de false al **setIsLoading** para quitar el loading.

```js
useEffect(() => {

    const controller = new AbortController();

    if (debouncedValue) {

      setIsLoading(true)

      searchPokemon(debouncedValue, controller.signal)
        .then(data => {
          setPokemon(data);
          setIsLoading(false);
        })
    }

    return () => controller.abort();
  }, [debouncedValue])
```

Una vez almacenado el pokemon, en el JSX colocamos la siguiente condición:
- Si el valor del estado isLoading es verdadero, mostramos el mensaje de **"Loading Results..."**
- Si el valor del estado isLoading es falso, mostramos el componente **Pokemon**, mandándole el pokemon.

```js
return (
    <div className="container">
      <h1> <span>Search Engine</span> whit <span>Debounce Effect</span> </h1>
      <Input {...{ value, onChange }} />
      {
        isLoading 
          ? <span>Loading Results...</span>
          : <Pokemon pokemon={pokemon}/>
      }
    </div>
  )
```

Y todo junto quedaría asi 👀:
```js
import { useEffect, useState } from 'react';

import { Input } from "./components/Input"
import { Pokemon } from "./components/Pokemon";

import { searchPokemon } from "./utils/searchPokemon";

import { ResponseAPI } from "./interface/pokemon";

const App = () => {

  const [pokemon, setPokemon] = useState<ResponseAPI | null>({} as ResponseAPI);
  const [isLoading, setIsLoading] = useState(false)

  const [value, setValue] = useState('');
  const onChange = (e: React.ChangeEvent<HTMLInputElement>) => setValue(e.target.value);

  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {

    const timer = setTimeout(() => setDebouncedValue(value), delay || 500)

    return () => clearTimeout(timer)
  }, [value, delay]);

  useEffect(() => {

    const controller = new AbortController();
    
    if (debouncedValue) {
      
      setIsLoading(true)

      searchPokemon(debouncedValue, controller.signal)
        .then(data => {
          setPokemon(data);
          setIsLoading(false);
        })
    }

    return () => controller.abort();
  }, [debouncedValue])


  return (
    <div className="container">
      <h1> <span>Search Engine</span> whit <span>Debounce Effect</span> </h1>
      <Input {...{ value, onChange }} />
      {
        isLoading 
          ? <span>Loading Results...</span>
          : <Pokemon pokemon={pokemon}/>
      }
      
    </div>
  )
}
export default App
```
Es mucha lógica en un solo componente cierto? 😱  
Ahora nos toca **refactorizar**!

&nbsp;


## 🎈 Limpiando la lógica de nuestro componente. <a id="13"></a>
Tenemos mucha lógica en nuestro componente por lo que es necesario separarla en diversos archivos: 
- Lógica para controlar el input.
- Lógica del debounce.
- Lógica para hacer la llamada a la API y manejar el pokemon.
Y como esta lógica hace uso de hooks como es useState y useEffect, entonces debemos colocarlos en un custom hook.

Lo primero sera crear una nueva carpeta **`src/hooks`**

### **1. Manejando la lógica para controlar el input.** <a id="14"></a>
Dentro de la carpeta **`src/hooks`** creamos el siguiente archivo **`useInput.ts`**
Y colocamos la lógica correspondiente al manejo del input.

```js
import { useState } from 'react';

export const useInput = (): [string, (e: React.ChangeEvent<HTMLInputElement>) => void] => {

    const [value, setValue] = useState('');

    const onChange = (e: React.ChangeEvent<HTMLInputElement>) => setValue(e.target.value);

    return [value, onChange]
}
```
Luego llamamos al **useInput** en el archivo **`App.tsx`**
```js
import { useEffect, useState } from 'react';

import { Input } from "./components/Input"
import { Pokemon } from "./components/Pokemon";

import { useInput } from "./hooks/useInput";

import { searchPokemon } from "./utils/searchPokemon";

import { ResponseAPI } from "./interface/pokemon";

const App = () => {

  const [value, onChange] = useInput();

  const [pokemon, setPokemon] = useState<ResponseAPI | null>({} as ResponseAPI);
  const [isLoading, setIsLoading] = useState(false)

  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {

    const timer = setTimeout(() => setDebouncedValue(value), delay || 500)

    return () => clearTimeout(timer)
  }, [value, delay]);

  useEffect(() => {

    const controller = new AbortController();
    
    if (debouncedValue) {
      
      setIsLoading(true)

      searchPokemon(debouncedValue, controller.signal)
        .then(data => {
          setPokemon(data);
          setIsLoading(false);
        })
    }

    return () => controller.abort();
  }, [debouncedValue])


  return (
    <div className="container">
      <h1> <span>Search Engine</span> whit <span>Debounce Effect</span> </h1>
      <Input {...{ value, onChange }} />
      {
        isLoading 
          ? <span>Loading Results...</span>
          : <Pokemon pokemon={pokemon}/>
      }
      
    </div>
  )
}
export default App
```

&nbsp;

### **2. Manejando la lógica para la llamada a la API.** <a id="15"></a>
Dentro de la carpeta **`src/hooks`** creamos el siguiente archivo **`useSearchPokemon.ts`**.  

Colocamos la lógica relacionada con hacer la petición a la API y mostrar el pokemon.

Este custom hook recibe como parámetro un string llamado **search**, que es el nombre del pokemon o el ID. Y ese parámetro se lo enviamos a la función que hace la llamada a la API **searchPokemon**

> 🚨 Nota: Observe la parte del If en el efecto, al final colocamos un else donde si el debouncedValue esta vació,nno haremos una llamada a la API y le mandamos el valor de un objeto vació a **setPokemon**

```js
import { useState, useEffect } from 'react';
import { ResponseAPI } from '../interface/pokemon';
import { searchPokemon } from '../utils/searchPokemon';

export const useSearchPokemon = (search: string) => {

    const [pokemon, setPokemon] = useState<ResponseAPI | null>({} as ResponseAPI);

    const [isLoading, setIsLoading] = useState(false)


    useEffect(() => {
        const controller = new AbortController();

        if (search) {

            setIsLoading(true);

            searchPokemon(search, controller.signal)
                .then(data => {
                    setPokemon(data);
                    setIsLoading(false);
                });

        }else { setPokemon({} as ResponseAPI) }

        return () => controller.abort();

    }, [search])

    return {
        pokemon,
        isLoading
    }
}
```
Luego llamamos al **useSearchPokemon** en el archivo **`App.tsx`**
```js
import { useEffect, useState } from 'react';

import { Input } from "./components/Input"
import { Pokemon } from "./components/Pokemon";

import { useInput } from "./hooks/useInput";
import { useSearchPokemon } from "./hooks/useSearchPokemon";

import { searchPokemon } from "./utils/searchPokemon";

import { ResponseAPI } from "./interface/pokemon";

const App = () => {

  const [value, onChange] = useInput();

  const [debouncedValue, setDebouncedValue] = useState(value);

  const { isLoading, pokemon } = useSearchPokemon(debouncedValue)

  useEffect(() => {

    const timer = setTimeout(() => setDebouncedValue(value), delay || 500)

    return () => clearTimeout(timer)
  }, [value, delay]);



  return (
    <div className="container">
      <h1> <span>Search Engine</span> whit <span>Debounce Effect</span> </h1>
      <Input {...{ value, onChange }} />
      {
        isLoading 
          ? <span>Loading Results...</span>
          : <Pokemon pokemon={pokemon}/>
      }
      
    </div>
  )
}
export default App
```

&nbsp;

### **3. Manejando la lógica para el efecto Debounce.** <a id="16"></a>
Dentro de la carpeta **`src/hooks`** creamos el siguiente archivo **`useDebounce.ts`** y colocamos toda la lógica para manejar el efecto debounce.

Este custom hook, recibe 2 parámetros:
- **value**: es el valor del estado del input. 
- **delay**: es la cantidad de tiempo que quieres retrasar la ejecución del debounce y es opcional.

> 🚨 Nota: la propiedad **delay** la usamos el segundo parámetro de la función setTimeout, donde en caso de que delay sea undefined, entonces el tiempo por defecto sera 500ms. 
> Y también, agregamos la propiedad delay al arreglo de dependencias del efecto.

```js
import { useState, useEffect } from 'react';

export const useDebounce = (value:string, delay?:number) => {

    const [debouncedValue, setDebouncedValue] = useState(value);

    useEffect(() => {

        const timer = setTimeout(() => setDebouncedValue(value), delay || 500)

        return () => clearTimeout(timer)
    }, [value, delay]);

    return debouncedValue
}
```
Luego llamamos al **useDebounce** en el archivo **`App.tsx`**
```js
import { useEffect, useState } from 'react';
import { Input } from "./components/Input"
import { Pokemon } from "./components/Pokemon";
import { useInput } from "./hooks/useInput";
import { useSearchPokemon } from "./hooks/useSearchPokemon";
import { useDebounce } from "./hooks/useDebounce";
import { searchPokemon } from "./utils/searchPokemon";
import { ResponseAPI } from "./interface/pokemon";

const App = () => {

  const [value, onChange] = useInput();

  const debouncedValue = useDebounce(value, 1000);  

  const { isLoading, pokemon } = useSearchPokemon(debouncedValue)

  return (
    <div className="container">
      <h1> <span>Search Engine</span> whit <span>Debounce Effect</span> </h1>
      
      <Input {...{ value, onChange }} />

      {
        isLoading 
          ? <span>Loading Results...</span>
          : <Pokemon pokemon={pokemon}/>
      }
      
    </div>
  )
}
export default App
```
**Y asi nuestro componente App.tsx quedo mas limpio y fácil de leer.** 🥳

&nbsp;


## 🎈 Conclusión. <a id="17"></a>
Todo el proceso que acabo de mostrar, es una de las formas en que se puede hacer un buscador con efecto debounce. 🔎

Espero haberte ayudado a entender como realizar este ejercicio,muchas gracias por llegar hasta aquí! 🤗  

Te invito a que comentes si es que conoces alguna otra forma distinta o mejor de como hacer un efecto debounce para un buscador. 🙌

&nbsp;

### 🎈 Código fuente. <a id="18"></a>
{%embed https://github.com/Franklin361/search-engine-debounce-effect %}