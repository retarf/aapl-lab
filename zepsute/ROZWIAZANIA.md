# Rozwiązania

## 01 — brak kolekcji w EE

`community.general` **nie jest** w obrazie `awx-ee`. Błąd: `couldn't resolve module/action
'community.general.timezone'`. Task nie doleciał nigdzie — zawiódł na etapie rozwiązywania
nazwy modułu, w EE.

Naprawa (trzy drogi, w kolejności trwałości):
1. zbudować własny EE z `community.general` (`ansible-builder`, `execution-environment.yml`) — u Klienta obraz ląduje w Automation Hubie;
2. dodać `collections/requirements.yml` do repo projektu — Controller instaluje kolekcje przy syncu (wolniej, ale bez budowania obrazu);
3. użyć modułu, który jest w `ansible.builtin` (tu: `ansible.builtin.file` + link do `/etc/localtime`, albo `command: timedatectl`).

**Diagnostyka u Klienta:** „module not found" przy kolekcji zewnętrznej to prawie zawsze
niezgodność EE, nie błąd playbooka. Pytanie brzmi „w jakim EE to leci i co ma w środku".

## 02 — zły interpreter Pythona

`ansible_python_interpreter: /usr/bin/python` — na Rocky 9 tej ścieżki nie ma (jest
`/usr/bin/python3`). SSH działa, host odpowiada, ale moduł nie ma czym się wykonać.

Naprawa: usunąć tę zmienną (domyślne wykrywanie działa) albo wskazać `/usr/bin/python3`.
Docelowo `interpreter_python = auto_silent` w `ansible.cfg`.

**Diagnostyka:** klasyk na starszych systemach, gdzie `/usr/bin/python` to Python 2 —
wtedy moduł nie pada, tylko zachowuje się dziwnie. Sprawdzaj `ansible_python_interpreter`
w inventory, group_vars i host_vars.

## 03 — brak idempotencji

`shell: echo … >> plik` dopisuje przy każdym uruchomieniu. Playbook zgłasza `changed`
zawsze, a plik rośnie. `mkdir -p` też jest zgłaszany jako zmiana, choć nic nie zmienia.

Naprawa: `ansible.builtin.file` (katalog), `ansible.builtin.lineinfile` (jedna linia, bez
duplikatów) albo `ansible.builtin.template` (cały plik z szablonu). Gdy `command`/`shell`
jest nieunikniony — `creates:`, `removes:` albo `changed_when:`.

**Diagnostyka:** uruchom playbook dwa razy. `changed` w drugim przebiegu to sygnał, że
task nie opisuje stanu, tylko wykonuje krok. Taki playbook nie nadaje się do harmonogramu.

## 04 — zielony job, nic nie działa

Pakiet nazywa się `httpdd` (literówka), ale `ignore_errors: true` przykrywa błąd, a
`failed_when: false` przy sprawdzeniu gwarantuje sukces niezależnie od wyniku. Ostatni task
ogłasza sukces bezwarunkowo. `PLAY RECAP` pokaże `ignored=1` — jedyny ślad.

Naprawa: usunąć `ignore_errors`; jeśli błąd naprawdę ma być tolerowany — obsłużyć go
(`register` + `when`), a weryfikację oprzeć na realnym warunku (`failed_when: wersja.rc != 0`).

**Diagnostyka:** czytaj `PLAY RECAP` — `ignored` i `rescued` to miejsca, gdzie ktoś
świadomie wyciszył błąd. Zielony job nie znaczy „działa", znaczy „nic nie zgłosiło błędu".

## 05 — niezdefiniowana zmienna

Szablon używa `app_workers`, którego nie ma w `vars`. Błąd wskazuje plik `.j2`, ale
przyczyna leży w playbooku — brakuje definicji.

Naprawa: dodać zmienną w `vars` / `group_vars` / `host_vars`, albo dać wartość domyślną
w szablonie: `{{ app_workers | default(4) }}`.

**Diagnostyka:** `'X' is undefined` zawsze czytaj jako „ktoś założył zmienną, której tu
nie ma". Sprawdź kolejność: `group_vars` → `host_vars` → `vars` → `--extra-vars`.
U Klienta częsta przyczyna: playbook działał w innym inventory, gdzie zmienna była.
