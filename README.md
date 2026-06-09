## Laboratorium 12
## Utworzenie własnej sieci mostkowej

```
docker network create --driver bridge lab12net
```
<img width="916" height="98" alt="image" src="https://github.com/user-attachments/assets/9834b0b3-1ee2-440f-8521-ffdcbf307ce4" />

## Przygotowanie katalogów i strony HTML 
<img width="439" height="303" alt="image" src="https://github.com/user-attachments/assets/eb3a3e89-90cf-4706-a7b7-2687d35cfaef" />

<img width="945" height="60" alt="image" src="https://github.com/user-attachments/assets/06328d4a-645e-4910-bcb4-0c2f69359c84" />

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

## Weryfikacja działania
### Serwery są w jednej sieci
```
docker network inspect lab12net
```
<img width="945" height="1315" alt="image" src="https://github.com/user-attachments/assets/380d85cb-1998-46eb-bd5c-c1bb825332a9" />

### Strony wyświetlają się poprawnie z zewnątrz

<img width="795" height="338" alt="image" src="https://github.com/user-attachments/assets/7062f847-ae17-439b-9302-b1e3a7743251" />
<img width="641" height="292" alt="image" src="https://github.com/user-attachments/assets/f5dde935-1692-4860-96cd-b5169e60a250" />
<img width="680" height="316" alt="image" src="https://github.com/user-attachments/assets/2c0bb19d-5a23-45b3-9321-3765feee07d4" />
<img width="638" height="336" alt="image" src="https://github.com/user-attachments/assets/78288349-8368-4dbd-a358-9d4ae83efca0" />

### Logi zapisują się na dysku hosta 
<img width="945" height="822" alt="image" src="https://github.com/user-attachments/assets/5e6d91ec-de74-4259-8123-50ba63b85763" />



```
```

```
