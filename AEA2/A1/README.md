# Activitat 1: Inicialització i rutes bàsiques amb Node i Express

## Objectiu

Crear un projecte **Node.js** amb **Express** que mostri l’estructura mínima d’un servidor REST i aprendre a comprovar els endpoints utilitzant **cURL** i eines gràfiques com **Postman** o **Thunder Client**. Aquesta activitat és la base per a futures pràctiques més avançades.

## Passos a realitzar

### 1. Creació i configuració del projecte

1. **Inicia el projecte**  
   - Obre un terminal, crea una nova carpeta i inicialitza el projecte amb els valors per defecte:
     ```bash
     mkdir salutacio-express
     cd salutacio-express
     npm init -y
     ```
   - Es generarà el fitxer `package.json` amb la configuració bàsica.

2. **Instal·la Express**  
   - Des del mateix directori executa:
     ```bash
     npm install express
     ```
   - Es crearà la carpeta `node_modules` i s’afegirà Express a la secció `"dependencies"` de `package.json`.

3. **Prepara l’arxiu principal**  
   - Crea un fitxer `app.js` i afegeix-hi aquest esquelet de servidor:
     ```javascript
     const express = require('express');
     const app     = express();
     const port    = 3000;

     app.use(express.json()); // Permet parsejar cossos JSON

     // Ruta GET i POST s'afegiran més endavant

     app.listen(port, () => {
       console.log(`Servidor escoltant a http://localhost:${port}`);
     });
     ```
   - Descarrega’t el servidor amb `node app.js` per comprovar que arrenca sense errors.

### 2. Definició de les rutes bàsiques

Crea dues rutes senzilles al servidor Express:

1. **GET /hola**  
   - Retorna una salutació al client:
     ```javascript
     app.get('/hola', (req, res) => {
       res.send('Benvingut/da al meu servidor Express!');
     });
     ```
   - Aquest endpoint respon sempre amb el mateix missatge.

2. **POST /echo**  
   - Retorna literalment el cos rebut en la petició com a JSON:
     ```javascript
     app.post('/echo', (req, res) => {
       res.json(req.body);
     });
     ```
   - Recorda que, per a rebre JSON, has d’enviar el capçalera `Content-Type: application/json` i un cos vàlid.

### 3. Verificació dels endpoints

Per garantir que el servidor funciona correctament, prova les rutes amb **cURL** i amb una eina gràfica (**Postman** o **Thunder Client**):

1. **Amb cURL**

   ```bash
   # GET /hola
   curl http://localhost:3000/hola

   # POST /echo (envia un JSON al cos)
   curl -X POST http://localhost:3000/echo \
     -H "Content-Type: application/json" \
     -d '{"nom":"Anna","edat":23}'
   ```

   - Comprova que la primera comanda et retorna el missatge de benvinguda.
   - La segona ha de retornar exactament el JSON que has enviat.

2. **Amb Postman o Thunder Client**

   - **Configura una variable d’entorn** anomenada `base_url` amb el valor `http://localhost:3000`.  
     En Postman s’afegeix des del tauler *Environments*; en Thunder Client, des del menú *Environments* → *Add*.
   - **Crea una col·lecció** amb dues peticions:
     - **GET** `{{base_url}}/hola`
     - **POST** `{{base_url}}/echo` amb cos `{"text":"Hola"}` i capçalera `Content-Type: application/json`
   - Desa la col·lecció (i l’entorn) i prova les peticions. Observa que la substitució de `{{base_url}}` facilita el canvi de servidor sense modificar cada URL.

3. **Evidències de les proves**  
   - Fes captura de pantalla dels resultats al terminal i a la teva eina gràfica.  
   - Assegura’t que es veu l’URL, el mètode, el cos (si escau) i la resposta.

## Criteris d’avaluació

- El projecte s’inicia correctament (fitxers `package.json` i `app.js`).
- Les rutes GET i POST funcionen segons l’enunciat.
- Es mostren captures de pantalla de cURL i Postman/Thunder Client amb els valors esperats.
- Les variables d’entorn i col·leccions s’utilitzen de forma correcta (en el cas de Postman/Thunder Client).

## Reforços i aclariments

- Si no recordes la sintaxi de les funcions d’Express, consulta la [documentació oficial de routing](https://expressjs.com/es/guide/routing.html).
- Pots afegir altres rutes per experimentar, però l’entrega només necessita `/hola` i `/echo`.
- Les variables d’entorn a Postman/Thunder Client ajuden a reutilitzar valors (com la base URL) i a no haver de canviar manualment tots els endpoints quan canvies de port o de servidor.
- En cas de dubte amb el format JSON, prova primer les peticions amb l’eina gràfica abans de fer-les amb cURL.
