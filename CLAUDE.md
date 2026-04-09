# CLAUDE.md — set-timezone

## Opis projektu

Rola Ansible konfigurująca strefę czasową i locale na systemach Debian/Ubuntu.
Namespace: `pl_rachuna_net`, nazwa roli: `set_timezone`.

## Struktura katalogów

Standardowy layout `ansible-galaxy role init`:

```
set-timezone/
├── defaults/main.yml        # Zmienne wejściowe (in_) z wartościami domyślnymi
├── handlers/main.yml        # Handlery (obecnie puste)
├── meta/main.yml            # Metadane Galaxy: autor, platformy, zależności
├── tasks/
│   ├── main.yml             # Punkt wejścia — dynamiczny include po os_family
│   └── locale_debian.yml    # Zadania locale + timezone dla Debian/Ubuntu
├── tests/main.yml           # Playbook testowy z asercjami
├── vars/main.yml            # Zmienne wewnętrzne var_ (obecnie puste)
├── .llm/                    # Kontekst i konwencje dla LLM
├── CONTRIBUTING.md
├── LICENSE
└── renovate.json
```

## Konwencja nazewnictwa zmiennych

| Prefix | Lokalizacja | Cel |
|--------|-------------|-----|
| `in_`  | `defaults/main.yml` lub przekazane z playbooka | Parametry wejściowe |
| `var_` | `vars/main.yml` lub `register` w taskach | Zmienne wewnętrzne roli |

Nigdy nie używaj zmiennych bez prefiksu wewnątrz roli.

## Zmienne wejściowe

| Zmienna       | Domyślna        | Opis |
|---------------|-----------------|------|
| `in_timezone` | `Europe/Warsaw` | Strefa czasowa (format IANA) |
| `in_locale`   | `pl_PL.UTF-8`  | Locale do `/etc/locale.gen` |
| `in_language` | `pl_PL:pl`     | Wartość `LANGUAGE` w `/etc/default/locale` |
| `in_lc_all`   | `pl_PL.UTF-8`  | Wartość `LC_ALL` w `/etc/default/locale` |

## Konwencje kodu

- **FQCN** — zawsze pełne nazwy modułów: `ansible.builtin.apt`, `ansible.builtin.copy` itp.
- **Platformy** — guard z `ansible_facts.os_family`; dispatch dynamiczny w `tasks/main.yml`
- **changed_when** — `false` tylko dla komend read-only; dla modyfikujących stan — warunek na rejestrze
- **stat > shell test** — zamiast `test -f` używaj `ansible.builtin.stat` + `when:`
- **copy > template** — dla prostych plików jednowartościowych preferuj `copy` z `content:`

## Wspierane platformy

- Debian: bullseye, bookworm
- Ubuntu: jammy
- Minimum Ansible: 2.12

## Commity

Standard [Conventional Commits](https://www.conventionalcommits.org/):
`feat:`, `fix:`, `docs:`, `chore:`, `refactor:`, `ci:`

## Testy

Playbook testowy: `tests/main.yml` — uruchamia rolę, a następnie weryfikuje asercjami:
- poprawność timezone (`timedatectl`, `/etc/timezone`)
- poprawność locale (`/etc/default/locale`, `locale -a`)
