## Plan: FastAPI backend tests toevoegen

Voeg een aparte top-level tests-map toe met pytest-tests voor de bestaande FastAPI-routes, inclusief happy paths en foutafhandeling, en maak de testomgeving reproduceerbaar door testafhankelijkheden en state-reset fixtures expliciet te maken.

**Steps**
1. Fase 1 - Testfundering opzetten: voeg een top-level `tests/` directory toe met `tests/conftest.py` en eventueel `tests/__init__.py`; definieer een `TestClient` fixture op basis van de bestaande module-level app en een fixture die de globale `activities` state voor elke test reset.
2. Fase 1 - Afhankelijkheden aanvullen: werk `requirements.txt` bij met testafhankelijkheden (`pytest`, en indien gewenst `pytest-asyncio`), zodat de workflowcontrole en lokale testuitvoering slagen. *parallel met stap 1*
3. Fase 2 - Happy-path route tests: voeg `tests/test_routes.py` toe met tests voor `GET /activities`, succesvolle `POST /activities/{activity_name}/signup` en succesvolle `DELETE /activities/{activity_name}/signup`. *depends on 1*
4. Fase 2 - Error-case tests: voeg `tests/test_error_handling.py` toe met tests voor niet-bestaande activiteiten (404), dubbele signup (400), en unregister zonder bestaande inschrijving (404). *depends on 1*
5. Fase 3 - Validatie: voer `pytest` uit en controleer dat tests deterministisch blijven bij meerdere runs (geen state leakage), en dat response statuscodes/boodschappen overeenkomen met huidige API-contracten. *depends on 2,3,4*
6. Fase 3 - Documentatie (optioneel maar aanbevolen): voeg in README een korte testinstructie toe (`pytest`) zodat nieuwe bijdragers direct de backend-tests kunnen draaien. *depends on 5*

**Relevant files**
- `/workspaces/skills-getting-started-with-github-copilot/src/app.py` — bron van routes en globale `activities` state; fixture reset en assertions moeten aansluiten op symbolen `app`, `activities`, `signup_for_activity`, `unregister_from_activity`.
- `/workspaces/skills-getting-started-with-github-copilot/requirements.txt` — toevoegen van testdependencies.
- `/workspaces/skills-getting-started-with-github-copilot/pytest.ini` — bestaande pytest-config (`pythonpath = .`) hergebruiken; waarschijnlijk geen wijziging nodig.
- `/workspaces/skills-getting-started-with-github-copilot/tests/conftest.py` — gedeelde fixtures voor `TestClient` en state reset.
- `/workspaces/skills-getting-started-with-github-copilot/tests/test_routes.py` — functionele endpoint tests (happy paths).
- `/workspaces/skills-getting-started-with-github-copilot/tests/test_error_handling.py` — foutscenario’s en edge cases.
- `/workspaces/skills-getting-started-with-github-copilot/README.md` — optionele sectie voor testuitvoering.

**Verification**
1. Draai `pytest -q` vanaf repository root en bevestig dat alle nieuwe tests slagen.
2. Draai dezelfde command nogmaals om te verifiëren dat fixture-reset state leakage voorkomt.
3. Controleer handmatig dat elke endpointcategorie minimaal één succespad en één foutpad heeft.
4. Controleer dat CI-verwachtingen (aanwezigheid tests-map + pytest dependency) worden afgedekt.

**Decisions**
- In scope: backend API-routes in `src/app.py` testen via HTTP-level tests met FastAPI `TestClient`.
- In scope: aparte top-level `tests/` directory (zoals door gebruiker gevraagd).
- Out of scope: frontend tests (`src/static/*`), refactor naar app factory, of contractwijzigingen in API-responses.
- Aanname: huidige routegedrag en statuscodes blijven leidend; tests codificeren bestaand gedrag.

**Further Considerations**
1. Bestandssplitsing: één testfile (`tests/test_app.py`) is compacter; twee files (`test_routes.py` + `test_error_handling.py`) is beter onderhoudbaar na groei. Aanbeveling: twee files.
2. Dependency-keuze: omdat handlers synchroon zijn is `pytest-asyncio` niet strikt vereist; toevoegen kan wel nuttig zijn voor toekomstige async tests. Aanbeveling: nu toevoegen voor future-proofing.
