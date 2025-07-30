# Activitat 3: Automatització de les proves

## Objectiu

Conèixer com es poden automatitzar les proves d’una API REST utilitzant les eines de testing treballades (cURL, Postman i Thunder Client) i les seves línies d’ordres. Es crearà una col·lecció amb scripts de comprovació, s’exportarà i s’executarà des del terminal per obtenir un informe de resultats.

## Tasques

### 1. Afegir scripts de test a les peticions

Utilitza la col·lecció creada a l’activitat 2 (amb les rutes CRUD) i afegeix un **script de test** a cada petició per validar la resposta. Alguns exemples en Postman:

```javascript
// comprova que el codi d'estat és el correcte
pm.test("Status 200", () => pm.response.code === 200);

// comprova que el cos conté un camp determinat
pm.test("Té propietat id", () => {
  const json = pm.response.json();
  pm.expect(json).to.have.property("id");
});
```

Pots adaptar els tests segons l’endpoint: per a `POST /items` comprova que el codi és 201 i que el cos retornat conté l’ID; per a `DELETE /items/:id` comprova que el codi és 204 i que el cos està buit.

### 2. Exportar la col·lecció i l’entorn

Quan tinguis tots els scripts definits:

1. **Exporta la col·lecció** des de Postman (`…` → **Export**) o des de Thunder Client (clic dret sobre la col·lecció → **Export**). Desarà un fitxer `.json` amb totes les peticions i scripts.
2. **Exporta també l’entorn d’execució** (`base_url`, `item_id`, etc.) com a fitxer `env.json` o equivalent.

### 3. Executar la col·lecció amb el CLI

#### 3.1 Postman CLI (Newman)

Instal·la Newman globalment si encara no el tens:

```bash
npm install -g newman
```

Executa la col·lecció amb l’entorn:

```bash
newman run ruta/al/fitxer-col.leccio.json -e ruta/al/fitxer-entorn.json \
  --reporters cli,html --reporter-html-export informe.html
```

Aquest comandament executa totes les peticions, mostra un resum al terminal i genera un informe HTML amb el número de proves passades i fallides. Revisa l’informe per detectar errors i corregeix els endpoints o els tests.

#### 3.2 Thunder Client CLI (`tc`) – opcional

Si utilitzes Thunder Client, pots instal·lar el CLI `tc` i executar col·leccions de manera similar:

```bash
# instal·la tc (si no està instal·lat)
npm install -g @ranga23/tc

# executa la col·lecció i genera informes HTML i JSON
tc --col "NomCol·lecció" --env "NomEntorn" --report html,json
```

Els informes s’emmagatzemen al directori `thunder-reports`. Thunder CLI també permet generar informes en format `xml` o `nunit`, compatibles amb diverses eines de CI/CD.

#### 3.3 Scripts amb cURL i Hurl (nivell avançat)

Com a alternativa, pots crear un script Bash (`proves.sh`) que invoqui totes les rutes amb `curl` i utilitzi `jq` per analitzar les respostes. Per a una aproximació més estructurada, prova **Hurl**, una eina basada en cURL que permet definir peticions i assertions en fitxers `.hurl`:

```bash
# instal·lació (exemple per Homebrew a macOS)
brew install hurl  # o consulta la documentació per a la teva plataforma

# exemple d'execució amb informes
hurl proves.hurl --report junit --report-file informe.xml
```

Hurl et genera un informe JUnit que pots integrar en pipelines d’integració contínua.

### 4. Documentació i entrega

1. Adjunta al repositori la col·lecció i l’entorn exportats (`.json`) i l’script `proves.sh` o els fitxers `.hurl` si n’has creat.
2. Inclou els informes generats (`informe.html`, `informe.xml`, etc.) per mostrar els resultats de les proves automatitzades.
3. Fes una captura de pantalla de l’execució del CLI (Newman o tc) on es vegi el resum amb el nombre de proves passades i fallides.

## Avaluació

- L’alumnat ha creat tests adequats per a cada endpoint i s’executen sense errors.
- S’han exportat i aportat la col·lecció i l’entorn correctament.
- L’execució amb Newman (o `tc`) mostra totes les proves verdes o justifica els vermells amb correccions posteriors.
- S’adjunten els informes de proves i s’explica breument la interpretació dels resultats.

## Recursos

- [Script de tests en Postman](https://learning.postman.com/docs/writing-scripts/script-references/test-examples/) — exemples de com escriure tests.
- [Documentació de Newman](https://github.com/postmanlabs/newman) — instal·lació i opcions d’execució.
- [Documentació del CLI de Thunder Client](https://www.thunderclient.com) — paràmetres i formats d’informes.
- [Hurl, el motor d’asserts sobre cURL](https://hurl.dev) — alternatives per a proves en línia de comandes.

Amb aquesta activitat t’introduiràs en la verificació automàtica d’APIs i t’acostumaràs a integrar les proves en el teu flux de treball.
