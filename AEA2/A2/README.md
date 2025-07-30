# Activitat 2: API CRUD amb dades en memòria

## Objectiu

Estendre el servidor de l’activitat 1 per gestionar una “llista d’elements” (tasques, contactes, notes…) utilitzant un array en memòria. Implementar totes les operacions CRUD i aprendre a provar-les amb **cURL** i amb **Postman/Thunder Client**, reutilitzant variables d’entorn i cossos de petició.

## Tasques

### 1. Preparació del projecte

1. **Reutilitza el projecte anterior** (`salutacio-express`) o crea’n un de nou amb `npm init -y` i `npm install express`.
2. **Actualitza l’arxiu principal (`app.js`)** o crea’n un de nou amb l’estructura següent:
   - Importa Express i activa el middleware `express.json()` per parsejar cossos JSON.
   - Declara una variable d’estat en memòria, per exemple:
     ```javascript
     let items  = [];      // llista d’elements
     let lastId = 0;       // per generar IDs únics
     ```
   - Exporta el servidor a `localhost:3000` amb `app.listen`.

### 2. Implementació de les rutes CRUD

Implementa els següents endpoints:

| Mètode i ruta      | Funció                                                                          |
|--------------------|----------------------------------------------------------------------------------|
| `GET /items`       | Retorna l’array complet d’elements (`items`) com a JSON.                        |
| `GET /items/:id`   | Cerca un element per `id` i el retorna. Si no existeix, retorna 404.            |
| `POST /items`      | Crea un nou element a partir del cos JSON rebut. Assigna un `id` autoincremental (`++lastId`), afegeix-lo a `items` i retorna’l amb codi 201. |
| `PUT /items/:id`   | Actualitza completament l’element amb identificador `id`. Substitueix les propietats pel cos rebut i respon amb l’element modificat. Si no es troba, 404. |
| `DELETE /items/:id`| Elimina l’element amb `id` i retorna codi 204 sense cos. Si no existeix, 404.   |

**Exemple de codi resumit** (no l’has de copiar literalment, adapta’l al teu estil):

```javascript
app.get('/items', (req, res) => {
  res.json(items);
});

app.get('/items/:id', (req, res) => {
  const item = items.find(i => i.id === +req.params.id);
  return item ? res.json(item) : res.status(404).json({ error: 'No trobat' });
});

app.post('/items', (req, res) => {
  const item = { id: ++lastId, ...req.body };
  items.push(item);
  res.status(201).json(item);
});

app.put('/items/:id', (req, res) => {
  const idx = items.findIndex(i => i.id === +req.params.id);
  if (idx === -1) return res.status(404).json({ error: 'No trobat' });
  items[idx] = { id: items[idx].id, ...req.body };
  res.json(items[idx]);
});

app.delete('/items/:id', (req, res) => {
  const exists = items.some(i => i.id === +req.params.id);
  if (!exists) return res.status(404).json({ error: 'No trotat' });
  items = items.filter(i => i.id !== +req.params.id);
  res.status(204).end();
});
```

### 3. Verificació de les rutes

#### 3.1 cURL

Prova tots els endpoints des del terminal. Exemple d’execució:

```bash
# Llista inicial (buida)
curl http://localhost:3000/items

# Crear un element
curl -X POST http://localhost:3000/items \
  -H "Content-Type: application/json" \
  -d '{"nom":"Comprar pa","fet":false}'

# Recuperar tots els elements
curl http://localhost:3000/items

# Recuperar per id (substitueix 1 pel teu id real)
curl http://localhost:3000/items/1

# Actualitzar per id
curl -X PUT http://localhost:3000/items/1 \
  -H "Content-Type: application/json" \
  -d '{"nom":"Comprar pa i llet","fet":true}'

# Eliminar per id
curl -X DELETE http://localhost:3000/items/1
```

Opcionalment, escriu un petit **script** (`proves.sh`) que invoqui totes les comandes en cadena i imprimeixi els resultats. Així pots repetir les proves ràpidament mentre desenvolupes.

#### 3.2 Postman o Thunder Client

1. **Definiu variables d’entorn**:
   - `base_url = http://localhost:3000`
   - `item_id` (deixa’l buit al principi; l’actualitzaràs més tard)
2. **Crea una col·lecció** amb cinc peticions (GET, GET per id, POST, PUT, DELETE) utilitzant `{{base_url}}` a l’URL:
   - A la petició de creació (`POST /items`), afegeix un script de test per capturar l’`id` retornat i desar-lo a la variable `item_id`. Exemple en Postman:
     ```javascript
     const id = pm.response.json().id;
     pm.environment.set('item_id', id);
     ```
   - A les rutes `GET /items/{{item_id}}`, `PUT /items/{{item_id}}` i `DELETE /items/{{item_id}}`, utilitza la variable `{{item_id}}`.
3. **Executa totes les peticions** seguint l’ordre i comprova que els resultats són coherents.  
   - En Thunder Client, revisa com afegir scripts amb la funció `tc.setVar()` si vols guardar el `id` a una variable de col·lecció.
4. **Captura de pantalla** dels resultats i de la configuració de variables.

### 4. Entrega

* El repositori o carpeta ha de contenir el fitxer `app.js` amb totes les rutes implementades.
* Inclou, si és possible, l’script de cURL (`proves.sh`) i la col·lecció exportada de Postman/Thunder Client (`.json` o `.thunder-collection`).
* Acompanya-ho amb captures de pantalla on es vegin les execucions i l’ús de variables.

## Avaluació

- Correcta implementació de totes les operacions CRUD.
- Gestió adequada d’identificadors i codis d’estat (201, 204, 404, etc.).
- Proves completes amb cURL i Postman/Thunder Client, incloent variables d’entorn i scripts.
- Claredat de la documentació i dels arxius entregats.

## Consells addicionals

- En tests més grans, la memòria pot no ser suficient; més endavant aprendrem a utilitzar bases de dades per persistir dades.
- Revisa els missatges d’error al terminal: Express mostrarà si tens errors de sintaxi o variables no definides.
- Recorda aturar i reiniciar el servidor (`Ctrl+C` i `node app.js`) cada vegada que modifiquis el codi.

Amb aquesta activitat consolidaràs els conceptes bàsics de desenvolupament d’APIs amb Node i Express i aprendràs a testar-los de manera eficient.
