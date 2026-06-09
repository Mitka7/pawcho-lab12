## Laboratorium 12
## Utworzenie własnej sieci mostkowej

```
docker network create --driver bridge lab12net
```
<img width="916" height="98" alt="image" src="https://github.com/user-attachments/assets/9834b0b3-1ee2-440f-8521-ffdcbf307ce4" />

#### Wykonanie tego polecenia ma na celu utworzenie nowej sieci o nazwie lab12net, bazującej na domyślnym sterowniku typu most (bridge). Działanie to bezpośrednio realizuje pierwszy podpunkt zadania obowiązkowego, który wymagał utworzenia i przygotowania sieci mostkowej o nazwie lab12net, do której w kolejnych krokach zostaną podłączone wszystkie trzy serwery.

## Przygotowanie katalogów i strony HTML 
<img width="439" height="303" alt="image" src="https://github.com/user-attachments/assets/eb3a3e89-90cf-4706-a7b7-2687d35cfaef" />

<img width="945" height="60" alt="image" src="https://github.com/user-attachments/assets/06328d4a-645e-4910-bcb4-0c2f69359c84" />

#### Wykonanie serii poleceń mkdir oraz polecenia echo ma na celu utworzenie struktury folderów w katalogu domowym (~/lab12) oraz wygenerowanie pliku index.html. Działanie to jest niezbędne do późniejszego zmapowania tych fizycznych zasobów jako wolumeny, co bezpośrednio realizuje dwa kluczowe wymogi zadania: przygotowuje gotową stronę internetową, którą w trybie "read-only" będą pokazywać wszystkie kontenery oraz tworzy trzy wyodrębnione podkatalogi, co zagwarantuje, że poszczególne serwery będą poprawnie i bezkolizyjnie zapisywać swoje logi we wskazanej lokalizacji na dysku laptopa.

## Uruchomienie kontenerów z mapowaniem portów i wolumenów
```
docker run -d --name web1 \
  --network lab12net \
  -p 8081:80 \
  --mount type=bind,source=$HOME/lab12/html,target=/usr/share/nginx/html,readonly \
  --mount type=bind,source=$HOME/lab12/web1_logs,target=/var/log/nginx \
  nginx:latest

```
```
docker run -d --name web2 \
  --network lab12net \
  -p 8082:80 \
  --mount type=bind,source=$HOME/lab12/html,target=/usr/share/nginx/html,readonly \
  --mount type=bind,source=$HOME/lab12/web2_logs,target=/var/log/nginx \
  nginx:latest

```

```
docker run -d --name web3 \
  --network lab12net \
  -p 8083:80 \
  --mount type=bind,source=$HOME/lab12/html,target=/usr/share/nginx/html,readonly \
  --mount type=bind,source=$HOME/lab12/web3_logs,target=/var/log/nginx \
  nginx:latest

```
<img width="945" height="544" alt="image" src="https://github.com/user-attachments/assets/83dfa863-eede-49b2-8278-e0a988ce3009" />

#### Wymóg podłączenia serwerów do jednej sieci zrealizowano poprzez dodanie flagi --network lab12net podczas uruchamiania każdego z kontenerów. Dostępność z sieci zewnętrznej zapewniono używając opcji -p (np. -p 8081:80), co pozwala na bezkolizyjne przekierowanie ruchu z trzech różnych portów hosta na domyślny port 80 Nginxa. Udostępnienie strony HTML z odpowiednimi uprawnieniami osiągnięto za pomocą opcji --mount type=bind, podpinając lokalny katalog z plikiem i dodając parametr readonly, aby zapobiec jego modyfikacji. Zapis logów do dedykowanych podkatalogów zrealizowano stosując drugie mapowanie --mount, które wiąże katalog /var/log/nginx z odpowiednio wyodrębnionymi folderami na dysku (web1_logs, web2_logs i web3_logs).


## Weryfikacja działania
### Serwery są w jednej sieci
```
docker network inspect lab12net
```
<img width="945" height="1315" alt="image" src="https://github.com/user-attachments/assets/380d85cb-1998-46eb-bd5c-c1bb825332a9" />

#### W celu weryfikacji poprawności konfiguracji wykonano polecenie docker network inspect lab12net. Zwrócony wynik jednoznacznie potwierdza (w sekcji dotyczącej kontenerów), że wszystkie trzy serwery (web1, web2 oraz web3) zostały pomyślnie podłączone do wspólnej sieci mostkowej i otrzymały w jej obrębie odpowiednie, wewnętrzne adresy IP.

### Strony wyświetlają się poprawnie z zewnątrz

<img width="795" height="338" alt="image" src="https://github.com/user-attachments/assets/7062f847-ae17-439b-9302-b1e3a7743251" />
<img width="641" height="292" alt="image" src="https://github.com/user-attachments/assets/f5dde935-1692-4860-96cd-b5169e60a250" />
<img width="680" height="316" alt="image" src="https://github.com/user-attachments/assets/2c0bb19d-5a23-45b3-9321-3765feee07d4" />
<img width="638" height="336" alt="image" src="https://github.com/user-attachments/assets/78288349-8368-4dbd-a358-9d4ae83efca0" />

#### Poprawne funkcjonowanie serwerów oraz ich dostępność z sieci zewnętrznej zweryfikowano wykorzystując polecenie curl (odpowiednio dla portów 8081, 8082 i 8083) oraz wywołując adresy lokalne w przeglądarce internetowej. Zwrócone w konsoli odpowiedzi tekstowe oraz dołączone zrzuty ekranu jednoznacznie potwierdzają, że wszystkie kontenery działają prawidłowo i pokazują podpiętą stronę HTML.

### Logi zapisują się na dysku hosta 
<img width="586" height="457" alt="image" src="https://github.com/user-attachments/assets/95d5c276-bb02-4bbe-bbee-dd4160373ef6" />

#### Weryfikację zapisu logów na dysku hosta przeprowadzono wykorzystując polecenie ls -al oddzielnie dla każdego z trzech dedykowanych podkatalogów (web1_logs, web2_logs oraz web3_logs). Uzyskane wyniki potwierdzają fizyczną obecność wygenerowanych plików access.log i error.log we wskazanych lokalizacjach, co jednoznacznie dowodzi poprawnego podpięcia wolumenów i prawidłowego zapisywania informacji o zdarzeniach przez kontenery bezpośrednio do systemu macierzystego.

<img width="945" height="822" alt="image" src="https://github.com/user-attachments/assets/5e6d91ec-de74-4259-8123-50ba63b85763" />

#### Ostateczną weryfikację mechanizmu zapisu logów przeprowadzono odczytując zawartość wygenerowanych plików za pomocą polecenia cat (na przykładzie katalogu ~/lab12/web1_logs). Zwrócone w konsoli szczegółowe wpisy z plików access.log oraz error.log stanowią ostateczny dowód na to, że serwery Nginx na bieżąco rejestrują zapytania HTTP oraz komunikaty systemowe, a mechanizm wolumenów bezbłędnie przekazuje te dane z wnętrza kontenerów wprost do plików docelowych w systemie macierzystym.

