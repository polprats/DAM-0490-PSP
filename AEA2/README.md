# Activitat d’ensenyament–aprenentatge: introducció a Node, Express i eines de verificació

Aquesta guia està pensada com a suport per a la nova activitat on l’alumnat crearà el seu primer servei **REST** amb **Node.js** i **Express** i aprendrà a provar‑lo amb *curl*, Postman i Thunder Client. El document combina teoria i pràctica pas a pas.

## 1 Node.js i l’ecosistema de paquets

### 1.1 Què és Node .js?

Node.js és un *runtime* de JavaScript basat en l’especificació V8 de Chrome. La seva arquitectura no bloquejant està basada en un bucle d’esdeveniments; això li permet executar moltes connexions simultànies sense crear un fil per a cada sol·licitud. El lloc oficial el descriu com un entorn **asíncron** i orientat a esdeveniments pensat per crear aplicacions de xarxa escalables. Com a conseqüència d’aquest model, Node evita bloquejos i pot reutilitzar el mateix procés per gestionar múltiples sol·licituds. Tot i que no utilitza fils per defecte, pot aprofitar diversos nuclis de processador mitjançant processos fill o clústers.

### 1.2 Gestó de paquets i el fitxer `package.json`

Node utilitza el **Node Package Manager (npm)** per descarregar i gestionar dependències. Quan inicialitzem un projecte amb `npm init` es crea el fitxer `package.json`, que conté metadades com el nom del projecte, la versió i les dependències. La finalitat principal de `package.json` és simplificar la gestió de biblioteques i scripts i permetre que `npm install` instal·li tots els paquets declarats. A més d’informació descriptiva (nom, autor, llicència), pot incloure scripts de *build* i dues seccions de dependències (`dependencies` i `devDependencies`).

### 1.3 Instal·lació de paquets

Per afegir una biblioteca només cal executar `npm install nom_paquet`. Si es vol incloure a les dependències del projecte (la configuració predeterminada en versions modernes de npm), `npm` escriurà l’entrada corresponent a `package.json`. També és possible instal·lar un paquet temporalment amb l’opció `--no-save`.

## 2 Inici del projecte i instal·lació d’Express

1. **Crear directori i inicialitzar npm**.  Obriu un terminal, creeu un directori per al projecte i executeu:

   ```bash
   mkdir api-alumnes
   cd api-alumnes
   npm init -y  # crea package.json amb valors per defecte
   ```

   Durant `npm init` se us demanarà l’``entry point``; utilitzeu `app.js` o `index.js` com a fitxer principal.

2. **Instal·lar Express**.  Executa:

   ```bash
   npm install express
   ```

   Això descarrega la dependència i l’afegeix a la secció `dependencies` de `package.json`.

## 3 Crear un servidor Express senzill

### 3.1 Estructura bàsica d’un servidor

Per definir rutes amb Express utilitzem el patró `app.METHOD(path, handler)`. `METHOD` és el verb HTTP (get, post, put, delete…), `path` és l’URL i `handler` és una funció que rep l’objecte de sol·licitud i resposta. El manual oficial de routing resumeix aquesta sintaxi. A continuació es mostra un exemple complet en el fitxer `app.js`:

```javascript
const express = require('express');
const app    = express();
const port   = 3000;

app.use(express.json()); // middleware per parsejar JSON

// Rutes de prova
app.get('/', (req, res) => {
  res.send('Servidor Express en funcionament');
});

app.post('/echo', (req, res) => {
  // retorna el cos rebut (req.body) com a resposta
  res.json(req.body);
});

app.put('/usuari/:id', (req, res) => {
  const { id } = req.params;
  // actualitza el recurs usuari amb id i retorna confirmació
  res.json({ id, updated: req.body });
});

app.delete('/usuari/:id', (req, res) => {
  const { id } = req.params;
  // elimina el recurs usuari
  res.json({ deleted: id });
});

app.listen(port, () => {
  console.log(`Servidor escoltant a http://localhost:${port}`);
});
```

L’exemple anterior utilitza els verbs **GET** per recuperar dades, **POST** per crear dades, **PUT** per actualitzar recursos i **DELETE** per eliminar‑los. Express permet utilitzar tants manipuladors com calgui i retorns personalitzats. Els exemples del manual oficial mostren com respondre a cada verb: `app.get('/')`, `app.post('/')`, `app.put('/user')` i `app.delete('/user')`.

### 3.2 Prova del servidor amb cURL

Després d’engegar el servidor (`node app.js`), pots provar els endpoints amb cURL:

* **GET**: `curl http://localhost:3000/` (ha de mostrar «Servidor Express en funcionament»).
* **POST**: `curl -X POST http://localhost:3000/echo -H "Content-Type: application/json" -d '{"nom":"Anna"}'` (retorna el JSON enviat).
* **PUT**: `curl -X PUT http://localhost:3000/usuari/1 -H "Content-Type: application/json" -d '{"email":"nou@example.com"}'`.
* **DELETE**: `curl -X DELETE http://localhost:3000/usuari/1`.

En l’activitat, caldrà capturar la sortida d’aquestes comandes i documentar‑la.

## 4 Bones pràctiques per organitzar una API REST

### 4.1 Nomenclatura de rutes

* **Utilitza noms** (substantius) per als recursos i evita verbs a l’URL. Les accions ja queden definides pels verbs HTTP. Per exemple, `/articles` representa el recurs articles; els verbs GET, POST, PUT i DELETE corresponen a obtenir, crear, actualitzar i eliminar respectivament.
* **Rutes en plural** per a col·leccions (per exemple `/users`) i singular per a un element específic (`/users/{id}`).
* **Hiérarchie clara**: utilitza el caràcter `/` per indicar relacions jeràrquiques, com `/customers/{customerId}/accounts` per accedir a comptes d’un client.
* **Evita una profunditat excessiva**: seguint l’article de bones pràctiques, es recomana no excedir dos o tres nivells de nesting. Si cal més profunditat, és millor retornar una URL del recurs fill dins de la resposta i mantenir l’endpoint senzill.

### 4.2 Verbs i cossos

* **GET** recupera recursos i no ha de modificar l’estat del servidor.
* **POST** s’utilitza per crear nous recursos.
* **PUT** actualitza un recurs existent, normalment substituint‑lo íntegrament.
* **DELETE** elimina el recurs especificat.
* **JSON com a format estàndard**: és el format recomanat per a peticions i respostes; molts marcs (inclòs Express) tenen funcionalitats integrades per parsejar i generar JSON.

### 4.3 Gestió d’errors i codis de resposta

Retorna codis HTTP significatius: `400 Bad Request` per errors de validació, `401 Unauthorized` o `403 Forbidden` per problemes d’autenticació, `404 Not Found` si el recurs no existeix i `500 Internal Server Error` per errors del servidor. Facilita informació clara en el cos de la resposta perquè els clients entenguin què ha fallat.

### 4.4 Organització interna del projecte

Encara que en l’exemple hem definit totes les rutes en un sol fitxer, en projectes reals és recomanable separar la lògica en mòduls:

* **`routes/`**: defineix els endpoints i delega la lògica a controladors.
* **`controllers/`**: conté funcions que processen la sol·licitud i responen.
* **`models/`**: descriu l’estructura de les dades o integra la base de dades.
* **`services/`**: encapsula la lògica de negoci reutilitzable.

Aquesta separació millora la llegibilitat i permet fer proves unitàries de manera independent.

## 5 Variables d’entorn i col·leccions en Postman

### 5.1 Variables a Postman

Postman permet emmagatzemar valors per reutilitzar‑los en múltiples peticions. Una variable és un nom simbòlic que es referencia amb la sintaxi `{{nom}}` i s’expandeix al valor assignat. Les variables es poden agrupar en diferents **scopes**: globals, de col·lecció, d’entorn, de dades o locals. Segons la documentació:

* **Variables globals**: disponibles en qualsevol col·lecció i espai de treball, útils per a prototipatge.
* **Variables de col·lecció**: associades a una col·lecció concreta, no depenen de l’entorn.
* **Variables d’entorn**: permeten canviar configuracions com la URL base segons si treballes en desenvolupament, preproducció o producció.
* **Variables locals o de dades**: definides dins d’una petició o prova; són temporals i no persisteixen un cop finalitza l’execució.

Per utilitzar variables, estableix‑les al gestor d’entorns de Postman i selecciona l’entorn actiu. Per exemple, pots definir `base_url = http://localhost:3000` i en les peticions escriure l’URL com `{{base_url}}/usuari/1`.

### 5.2 Crear i editar variables

La documentació explica que pots crear una variable seleccionant text en una sol·licitud (adreça, paràmetres, capçaleres…) i triant **Set as variable**. També és possible afegir o modificar variables des del tauler d’environments: un usuari amb permís d’editor pot definir el nom, tipus (per defecte o secret) i valor inicial i actual i guardar‑la. L’ús de variables de tipus secret o *vault* serveix per a dades sensibles, ja que Postman les oculta i no les sincronitza amb el núvol.

### 5.3 Executar col·leccions a Postman

Un cop tens una col·lecció amb diverses peticions, la pots executar de diferents maneres:

* **Collection Runner**: permet executar manualment totes les peticions d’una col·lecció en l’ordre que decideixis. És útil per a un petit nombre de sol·licituds i no requereix repetició regular.
* **Programar execucions**: Postman permet planificar execucions periòdiques o monitoritzar l’API.
* **Postman CLI (Newman)**: el CLI de Postman permet executar col·leccions des del terminal i automatitzar‑les a pipelines CI/CD. Cal instal·lar la eina, generar una clau API i copiar l’ordre generada. El CLI executa la col·lecció localment i envia els resultats al núvol.

## 6 Variables d’entorn i col·leccions a Thunder Client

Thunder Client és una extensió lleugera per a VS Code que també permet definir variables i col·leccions. La seva documentació explica que es poden utilitzar variables d’entorn amb la sintaxi `{{variable}}` en paràmetres, capçaleres i cos de les peticions. Els entorns tenen diferents prioritats:

1. **Variables del sistema operatiu**: accessibles com a globals (format `{{VARIABLE}}`).
2. **Global Environment**: fitxer `tc_env_global.json` compartit per totes les col·leccions.
3. **Local Environment**: guarda secrets i tokens només al teu ordinador.
4. **Arxius `.env`**: en la versió de pagament, es poden enllaçar fitxers `.env` a un entorn.
5. **Entorn actiu**: cal activar l’entorn des del menú perquè les variables s’apliquin.
6. **Variables de col·lecció i petició**: variables específiques definides mitjançant scripts (`tc.setVar(...)`).

### 6.1 Thunder Client CLI

A partir de la versió 2.3, Thunder Client disposa d’un **CLI** anomenat `tc` que permet executar col·leccions i generar informes des del terminal. La guia oficial indica que:

* Per executar totes les peticions d’una col·lecció: `tc --col 'NomCol·lecció'`.
* Per executar diverses col·leccions: `tc --col "Col1,Col2"`.
* Per executar totes les col·leccions: `tc --col all`.
* El paràmetre `--env 'NomEntorn'` permet triar l’entorn.
* Es poden generar **informes** en diferents formats (cli, csv, html, json, xml o nunit) amb l’opció `--report`. Per exemple, `tc --col 'User' --report xml,html` executa la col·lecció i desa els informes a `thunder-reports`.

Aquest CLI és una alternativa a Newman per a Postman i permet integrar proves en pipelines de CI/CD.

## 7 Recomanacions finals

* **Documentació i versions**: inclou un fitxer `README.md` al teu projecte explicant com instal·lar dependències, engegar el servidor i provar els endpoints. Mantén actualitzats els números de versió a `package.json` i utilitza `semver` per evitar incompatibilitats.
* **Control de fonts**: utilitza Git per versionar el codi. Desa les col·leccions i entorns de Postman/Thunder Client al repositori per poder reproduir les proves.
* **Automatització**: quan ja domines Postman o Thunder Client, crea scripts de proves (test scripts) que comprovin automàticament els codis de resposta i el contingut. Exporta la col·lecció i integra‑la amb Newman o `tc` per obtenir informes d’assaig.
* **Seguretat**: no incloguis secrets ni claus API al codi; emmagatzema’ls en variables d’entorn (a Postman o Thunder Client) o utilitza fitxers `.env` que no es versionin. Considera utilitzar un gestor de secrets.

Amb aquesta base teòrica i pràctica l’alumnat pot crear un servei REST complet amb Node .js i Express i aprendre a verificar‑lo amb cURL, Postman i Thunder Client, utilitzant col·leccions, variables i automatització de proves.
