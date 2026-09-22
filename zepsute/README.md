# Zepsute playbooki — ćwiczenie diagnostyczne

Pięć playbooków z błędami, które realnie spotyka się w cudzym kodzie. Uruchamiaj je
z AWX (Job Template → Playbook → wybierz z `zepsute/`), czytaj output i **postaw diagnozę,
zanim zajrzysz do rozwiązań**.

Przy każdym odpowiedz sobie na trzy pytania:

1. **Gdzie** task się wykonał — EE czy host docelowy?
2. **Jako kto** i z jakimi uprawnieniami?
3. **Czego zabrakło** — modułu, pakietu, zmiennej, uprawnienia, a może niczego (i na tym polega problem)?

| Playbook | Wskazówka |
|---|---|
| `01-kolekcja.yml` | pada od razu, zanim cokolwiek poleci na hosta |
| `02-interpreter.yml` | host odpowiada, ale moduł nie |
| `03-nieidempotentny.yml` | **uruchom dwa razy** i porównaj `PLAY RECAP` oraz wynik |
| `04-ukryty-blad.yml` | kończy się na zielono — i to jest błąd |
| `05-zmienna.yml` | komunikat wskazuje plik, ale przyczyna jest gdzie indziej |

Rozwiązania: [ROZWIAZANIA.md](./ROZWIAZANIA.md) — zajrzyj dopiero po własnej próbie.
