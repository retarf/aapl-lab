# aapl-lab

Playbooki do nauki Ansible / Automation Controllera. Uruchamiane z AWX
(Project → Job Template) przeciw dwóm kontenerom Rocky Linux 9 (`target1`, `target2`).

Generyczne ćwiczenia — bez danych, nazw i adresów z jakiegokolwiek środowiska produkcyjnego.

| Playbook | Co pokazuje |
|---|---|
| `01-gdzie-jestem.yml` | gdzie i jako kto wykonuje się task (EE vs host docelowy) |
| `02-plik-w-etc.yml` | zapis do `/etc` z szablonu Jinja2, handler, idempotencja |
| `03-pakiet.yml` | instalacja pakietu, warunek na rodzinie systemu, pętla |

Katalog [`zepsute/`](./zepsute/README.md) — pięć playbooków z typowymi błędami do samodzielnej diagnozy.
