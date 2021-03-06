# 驴C贸mo crear un buscador con efecto "debounce"? 馃攷

Tabla de contenido
> 馃搶 [Introducci贸n.](#1)  
> 馃搶 [Tecnolog铆as a utilizar.](#2)   
> 馃搶 [驴Qu茅 es el efecto "Debounce"?](#3)  
> 馃搶 [Creando el proyecto.](#4)   
> 馃搶 [Primeros pasos.](#5)   
> 馃搶 [Creando el input.](#6)   
> 馃搶 [Manejando el estado del input.](#7)   
> 馃搶 [Creando la funci贸n para la petici贸n a la API.](#8)   
> 馃搶 [Creando el efecto Debounce.](#9)   
> 馃搶 [Haciendo la llamada a la API.](#10)   
> 馃搶 [Creando el componente Pokemon.tsx.](#11)   
> 馃搶 [Usando nuestro componente Pokemon.](#12)   
> 馃搶 [Limpiando la l贸gica de nuestro componente.](#13)   
>> 馃搶 [1. Manejando la l贸gica para controlar el input.](#14)   
>> 馃搶 [2. Manejando la l贸gica para la llamada a la API.](#15)   
>> 馃搶 [3. Manejando la l贸gica para el efecto Debounce.](#16)      
>
> 馃搶 [Conclusi贸n.](#17)   
>> 馃搶 [C贸digo fuente.](#18)   

&nbsp;

## 馃巿 Introducci贸n <a id="1"></a>

El prop贸sito de este post es ense帽ar una manera sencilla de como realizar un peque帽o buscador con un efecto debounce. 
Dicho proyecto puede extenderse de muchas maneras, pero tratare de hacerlo algo b谩sico pero eficiente.

> 馃毃 Nota: Este post requiere que sepas las bases de **React con TypeScript (hooks b谩sicos y peticiones con fetch)**.
>

Cualquier tipo de Feedback es bienvenido, gracias y espero disfrutes el articulo.馃

&nbsp;

## 馃巿 Tecnolog铆as a utilizar. <a id="2"></a>

- 鈻讹笍 React JS (version 18)
- 鈻讹笍 Vite JS
- 鈻讹笍 TypeScript
- 鈻讹笍 [Pokemon API](https://pokeapi.co)
- 鈻讹笍 CSS vanilla (Los estilos los encuentras en el repositorio al final de este post) 

&nbsp;

## 馃巿 驴Qu茅 es el efecto "Debounce"? <a id="3"></a>

El efecto rebote (**debounce**) es cuando no se ejecutan al momento de su invocaci贸n. En lugar de eso, su ejecuci贸n es retrasada por un periodo predeterminado de tiempo. Si la misma funci贸n es invocada de nuevo, la ejecuci贸n previa es cancelada y el tiempo de espera se reinicia.

&nbsp;

## 馃巿 Creando el proyecto. <a id="4"></a>

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
Despu茅s abrimos el proyecto en un editor de c贸digo (en mi caso VS code).
```bash
code .
```

&nbsp;

## 馃巿 Primeros pasos. <a id="5"></a>

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

Deber铆a de verse as铆 馃憖:

![Title](https://res.cloudinary.com/dnxchppfm/image/upload/v1655994217/posts/search-debounce/01_sp9sa9.png)

&nbsp;

## 馃巿 Creando el input. <a id="6"></a>

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

Deber铆a de verse as铆 馃憖:

![Input](https://res.cloudinary.com/dnxchppfm/image/upload/v1655994217/posts/search-debounce/02_avk6ld.png)

&nbsp;

## 馃巿 Manejando el estado del input. <a id="7"></a>

> 馃毃 Nota: Esta **NO** es la 煤nica forma para realizar este ejercicio, solo es una opci贸n! Si tienes una mejor manera, me gustar铆a que la compartieras por los comentarios por favor. 馃槍
>

En este caso voy a manejar el estado del input en un nivel superior, o sea, el componente App del archivo **`App.tsx`**

Esto lo haremos, debido a que necesitamos el valor del input disponible en **`App.tsx`**, ya que ahi se har谩 la petici贸n a la API y el efecto debounce.

1 - Primero creamos el estado para manejar el valor del input. 
```js
const [value, setValue] = useState('');
```
2 - Creamos una funci贸n para que actualice el estado del input cuando el input haga un cambio.  
Esta funci贸n recibe como par谩metro el evento que emite el input, de dicho evento obtendremos la propiedad **target** y luego la propiedad **value**, la cual es la que mandaremos a nuestro estado.
```js
const onChange = (e: React.ChangeEvent<HTMLInputElement>) => setValue(e.target.value); 
```
3 - Por consiguiente, toca mandar la funci贸n y el valor del estado al input.
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
4 - En el componente Input agregamos una interfaz para recibir las propiedades por par谩metro en el archivo **`Input.tsx`**.
```js
interface Props {
   value: string;
   onChange: (e: React.ChangeEvent<HTMLInputElement>) => void;
}
```
5 - Desestructuramos las propiedades y las agregamos al input.  
La funci贸n onChange, la colocamos en la propiedad **onChange** del input y lo mismo con el la  value propiedad **value**.
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
Y as铆 ya tenemos controlado el estado de nuestro input. 馃コ

&nbsp;

## 馃巿 Creando la funci贸n para la petici贸n a la API. <a id="8"></a>
Ahora creamos la carpeta **`src/utils`** y dentro colocamos un archivo llamado **`searchPokemon.ts`** y agregamos la siguiente funci贸n para hacer la petici贸n, y buscar un pokemon por su nombre o ID.

> 馃毃 Nota: La respuesta de la API tiene m谩s propiedades de las que se representa en la interfaz **ResponseAPI**
>

Esta funci贸n recibe dos par谩metros: 
- **pokemon**: es el nombre o ID del pokemon.
- **signal**: permite establecer escuchadores de eventos. En otras palabras, nos ayudara a cancelar la petici贸n HTTP cuando el componente se desmonte o haga un cambio en el estado.  

Esta funci贸n retorna la data del pokemon si todo sale bien o null si algo sale mal.

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

## 馃巿 Creando el efecto Debounce. <a id="9"></a>
En el archivo **`App.tsx`** creamos un estado, que nos servir谩 para guardar el valor del input.

```jsx
const [debouncedValue, setDebouncedValue] = useState();
```
Como estado inicial le mandamos el valor del estado del input (**value**).
```jsx
const [value, setValue] = useState('');

const onChange = (e: React.ChangeEvent<HTMLInputElement>) => setValue(e.target.value);

const [debouncedValue, setDebouncedValue] = useState(value);
```
Ahora, creamos un efecto para que cuando el valor del input cambie, ejecutamos la funci贸n **setTimeout** que actualizara el estado del **debouncedValue** enviando el nuevo valor del input, despu茅s de 1 segundo, y asi obtendremos la palabra clave o sea el pokemon, para hacer la petici贸n a la API.

Al final del efecto, ejecutamos el m茅todo de limpieza, que consiste en limpiar la funci贸n **setTimeout**, es por eso que la guardamos en una constante llamada **`timer`**

```js
useEffect(() => {
    const timer = setTimeout(() => setDebouncedValue(value), 1000)

    return () => clearTimeout(timer)
}, [value]);
```
Entonces por el momento nuestro archivo App.tsx quedar铆a de la siguiente manera: 
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

## 馃巿 Haciendo la llamada a la API. <a id="10"></a>
Una vez que tenemos el valor del input ya con el efecto debounce, toca hacer la llamada a la API.  
Para eso usaremos al funci贸n que creamos con anterioridad, **`searchPokemon.tsx`**.

Para ello, vamos a usar un efecto.
Primero creamos el **`controller`** el cual es el que nos ayudara a cancelar la petici贸n HTTP, como mencionamos antes
Dentro del controller tenemos dos propiedades que nos interesan: 
- **abort()**: al ejecutarse, cancela la petici贸n.
- **signal**: mantiene la conexi贸n entre el controller y el la petici贸n para saber cual se debe cancelar.

El **abort()** lo ejecutamos al final, al momento de que se desmonte el componente.

```jsx
useEffect(() => {
    
    const controller = new AbortController();
    
    return () => controller.abort();

  }, []);
```
La dependencia de este efecto sera el valor del debouncedValue, ya que cada vez que cambie este valor, debemos hacer una nueva petici贸n para buscar al nuevo pokemon.
```js
useEffect(() => {
    const controller = new AbortController();

    return () => controller.abort();

  }, [debouncedValue])
```
Hacemos una condici贸n, en la que solo si el existe el valor de **debouncedValue** y tiene alguna palabra o numero, haremos la petici贸n.
```js
useEffect(() => {
    const controller = new AbortController();
    
    if (debouncedValue) {

    }

    return () => controller.abort();
  }, [debouncedValue])
```

Dentro del if llamamos la funci贸n de **searchPokemon** y le mandamos el valor de **debouncedValue** y tambi茅n la propiedad **signal** del **controller**

```js
useEffect(() => {
    const controller = new AbortController();

    if (debouncedValue) {
        searchPokemon(debouncedValue, controller.signal)
    }

    return () => controller.abort();
  }, [debouncedValue])
```
Y como la funci贸n **searchPokemon** regresa una promesa y dentro del efecto no es permitido usar **async/await**, usaremos **.then** para resolver la promesa y obtener el valor que retorna.
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
Al final deber铆a de verse asi. 馃憖
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

## 馃巿 Creando el componente Pokemon.tsx. <a id="11"></a>

1 - Primero creamos el componente funcional vaci贸.

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
3 - Hacemos una evaluaci贸n donde:
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

Deber铆a de verse asi si esta cargando 馃憖:
![Loading](https://res.cloudinary.com/dnxchppfm/image/upload/v1655994217/posts/search-debounce/03_cugi7i.png)

Deber铆a de verse asi cuando no hay resultados 馃憖:
![No results](https://res.cloudinary.com/dnxchppfm/image/upload/v1655994217/posts/search-debounce/05_olggt6.png)

Deber铆a de verse asi hay un pokemon 馃憖:
![Pokemon](https://res.cloudinary.com/dnxchppfm/image/upload/v1655994217/posts/search-debounce/04_zomwzc.png)


4 - Y ahora por ultimo, agregamos una ultima condici贸n, en donde evaluamos si el pokemon existe (o sea que no es nula) y si es un objeto vaci贸 retornamos un fragmento.

>Esto es as铆 ya que el estado inicial para almacenar a los pokemon sera un objeto vac铆o "**{}**".  
>Si no colocaremos esa condici贸n, entonces la inicio de nuestra app, incluso sin haber tecleado nada en el input, ya nos aparecer铆a el mensaje de "No results", y la idea es que aparezca despu茅s de que hayamos tecleado algo en el input y se haya hecho la llamada a la API.
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

Asi quedar铆a nuestro componente pokemon, es hora de usarlo. 馃槍

&nbsp;

## 馃巿 Usando nuestro componente Pokemon. <a id="12"></a>
En el archivo App.tsx agregaremos 2 nuevos estados:
- Para almacenar el pokemon encontrado, que tendr谩 un valor inicial de un objeto vac铆o.
- Para manejar un loading en lo que se hace la llamada a la API, que tendr谩 un valor inicial de falso.

```js
const [pokemon, setPokemon] = useState<ResponseAPI | null>({} as ResponseAPI);
const [isLoading, setIsLoading] = useState(false)
```
Ahora dentro del efecto donde hacemos la llamada a la API mediante la funci贸n **`searchPokemon`**, antes de hacer la llamada enviamos el valor de true al **setIsLoading** para activar el loading.  
Despu茅s, una vez obtenida la data dentro del **.then** le enviamos dicha data al **setPokemon** (el cual puede ser el pokemon o un valor nulo).
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

Una vez almacenado el pokemon, en el JSX colocamos la siguiente condici贸n:
- Si el valor del estado isLoading es verdadero, mostramos el mensaje de **"Loading Results..."**
- Si el valor del estado isLoading es falso, mostramos el componente **Pokemon**, mand谩ndole el pokemon.

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

Y todo junto quedar铆a asi 馃憖:
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
Es mucha l贸gica en un solo componente cierto? 馃槺  
Ahora nos toca **refactorizar**!

&nbsp;


## 馃巿 Limpiando la l贸gica de nuestro componente. <a id="13"></a>
Tenemos mucha l贸gica en nuestro componente por lo que es necesario separarla en diversos archivos: 
- L贸gica para controlar el input.
- L贸gica del debounce.
- L贸gica para hacer la llamada a la API y manejar el pokemon.
Y como esta l贸gica hace uso de hooks como es useState y useEffect, entonces debemos colocarlos en un custom hook.

Lo primero sera crear una nueva carpeta **`src/hooks`**

### **1. Manejando la l贸gica para controlar el input.** <a id="14"></a>
Dentro de la carpeta **`src/hooks`** creamos el siguiente archivo **`useInput.ts`**
Y colocamos la l贸gica correspondiente al manejo del input.

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

### **2. Manejando la l贸gica para la llamada a la API.** <a id="15"></a>
Dentro de la carpeta **`src/hooks`** creamos el siguiente archivo **`useSearchPokemon.ts`**.  

Colocamos la l贸gica relacionada con hacer la petici贸n a la API y mostrar el pokemon.

Este custom hook recibe como par谩metro un string llamado **search**, que es el nombre del pokemon o el ID. Y ese par谩metro se lo enviamos a la funci贸n que hace la llamada a la API **searchPokemon**

> 馃毃 Nota: Observe la parte del If en el efecto, al final colocamos un else donde si el debouncedValue esta vaci贸,nno haremos una llamada a la API y le mandamos el valor de un objeto vaci贸 a **setPokemon**

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

### **3. Manejando la l贸gica para el efecto Debounce.** <a id="16"></a>
Dentro de la carpeta **`src/hooks`** creamos el siguiente archivo **`useDebounce.ts`** y colocamos toda la l贸gica para manejar el efecto debounce.

Este custom hook, recibe 2 par谩metros:
- **value**: es el valor del estado del input. 
- **delay**: es la cantidad de tiempo que quieres retrasar la ejecuci贸n del debounce y es opcional.

> 馃毃 Nota: la propiedad **delay** la usamos el segundo par谩metro de la funci贸n setTimeout, donde en caso de que delay sea undefined, entonces el tiempo por defecto sera 500ms. 
> Y tambi茅n, agregamos la propiedad delay al arreglo de dependencias del efecto.

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
**Y asi nuestro componente App.tsx quedo mas limpio y f谩cil de leer.** 馃コ

&nbsp;


## 馃巿 Conclusi贸n. <a id="17"></a>
Todo el proceso que acabo de mostrar, es una de las formas en que se puede hacer un buscador con efecto debounce. 馃攷

Espero haberte ayudado a entender como realizar este ejercicio,muchas gracias por llegar hasta aqu铆! 馃  

Te invito a que comentes si es que conoces alguna otra forma distinta o mejor de como hacer un efecto debounce para un buscador. 馃檶

&nbsp;

### 馃巿 C贸digo fuente. <a id="18"></a>
{%embed https://github.com/Franklin361/search-engine-debounce-effect %}