1.2 Observer une requête simple

URL : https://httpbin.org/get
Code de statut :
200 OK

Headers envoyés :

authority: httpbin.org
method: GET
path: /get
scheme: https
Accept
Accept-Encoding
Accept-Language
User-Agent
Content-Type de la réponse :
application/json

1.3 Tester différentes méthodes

Requête GET

javascript
fetch('https://httpbin.org/get')
.then(r => r.json())
.then(console.log);

Résultat observé

Une promesse (Promise) est créée.
Le serveur répond correctement.
La réponse est au format JSON.
Méthode HTTP utilisée : GET
Code de statut : 200 OK
Exemple de réponse :

json
{
"args": {},
"headers": {...},
"origin": "...",
"url": "https://httpbin.org/get"
}

Requête POST
javascript
fetch('https://httpbin.org/post', {
method: 'POST',
headers: {
'Content-Type': 'application/json'
},
body: JSON.stringify({
name: 'John',
age: 30
})
})
.then(r => r.json())
.then(console.log);

Résultat

Méthode HTTP : POST

Code de statut : 200 OK

Header envoyé :
http
Content-Type: application/json

Corps envoyé :

{
  "name": "John",
  "age": 30
}
Réponse reçue :
{
  "json": {
    "name": "John",
    "age": 30
  }
}
1.4 Observer les codes de statut

| https://httpbin.org/status/200 | 200 OK | Succès |
| https://httpbin.org/status/404 | 404 Not Found | Ressource introuvable |
| https://httpbin.org/status/500 | 500 Internal Server Error | Erreur serveur |
| https://httpbin.org/redirect/3 | 302 → 302 → 302 → 200 | Redirections puis succès |
2-1 >
curl https://httpbin.org/get
{
"args": {},
"headers": {
"Accept": "/",
"Host": "httpbin.org",
"User-Agent": "curl/8.13.0",
"X-Amzn-Trace-Id": "Root=1-69f0e95c-3e4d28ed7a7a2a06388b76fe"
},
"origin": "41.250.6.117",
"url": "https://httpbin.org/get"
}

curl -i https://httpbin.org/get
HTTP/1.1 200 OK
Date: Tue, 28 Apr 2026 17:07:50 GMT
Content-Type: application/json
Content-Length: 254
Connection: keep-alive
Server: gunicorn/19.9.0
Access-Control-Allow-Origin: *
Access-Control-Allow-Credentials: true

{
"args": {},
"headers": {
"Accept": "/",
"Host": "httpbin.org",
"User-Agent": "curl/8.13.0",
"X-Amzn-Trace-Id": "Root=1-69f0e966-25bf1b6633592a4812baca76"
},
"origin": "41.250.6.117",
"url": "https://httpbin.org/get"
}

curl -v https://httpbin.org/get
Host httpbin.org:443 was resolved.
IPv6: (none)
IPv4: 54.198.84.224, 23.23.43.241, 3.234.199.80, 44.199.179.5, 3.91.112.114, 98.83.84.78, 98.84.87.4, 52.73.30.72
Trying 54.198.84.224:443...
schannel: disabled automatic use of client certificate
ALPN: curl offers http/1.1
ALPN: server accepted http/1.1
Connected to httpbin.org (54.198.84.224) port 443
using HTTP/1.x
GET /get HTTP/1.1
Host: httpbin.org
User-Agent: curl/8.13.0
Accept: /

Request completely sent off
< HTTP/1.1 200 OK
< Date: Tue, 28 Apr 2026 17:07:58 GMT
< Content-Type: application/json
< Content-Length: 254
< Connection: keep-alive
< Server: gunicorn/19.9.0
< Access-Control-Allow-Origin: *
< Access-Control-Allow-Credentials: true
<
{
"args": {},
"headers": {
"Accept": "/",
"Host": "httpbin.org",
"User-Agent": "curl/8.13.0",
"X-Amzn-Trace-Id": "Root=1-69f0e96e-4ea8ef4a62c0fe665fdf5c3f"
},
"origin": "41.250.6.117",
"url": "https://httpbin.org/get"
}
Connection #0 to host httpbin.org left intact
-i affiche les headers + la réponse, tandis que -v montre tous les détails techniques de la connexion (debug complet).

2-2

curl -X POST -d "name=John&email=john@example.com" \https://httpbin.org/post

curl: (3) URL rejected: Port number was not a decimal number between 0 and 65535

2-3 >
curl https://httpbin.org/redirect/3

<title>Redirecting...</title>
Redirecting...
You should be redirected automatically to target URL: /relative-redirect/2. If not click the link.

curl -L https://httpbin.org/redirect/3
{
"args": {},
"headers": {
"Accept": "/",
"Host": "httpbin.org",
"User-Agent": "curl/8.13.0",
"X-Amzn-Trace-Id": "Root=1-69f0f0bd-289d479e1a4dbbc23a53b10a"
},
"origin": "41.250.6.117",
"url": "https://httpbin.org/get"
}

2-4
curl -o image.png https://httpbin.org/image/png
% Total % Received % Xferd Average Speed Time Time Time Current
Dload Upload Total Spent Left Speed
100 8090 100 8090 0 0 9834 0 --:--:-- --:--:-- --:--:-- 9865

curl -O https://example.com/fichier.pdf
% Total % Received % Xferd Average Speed Time Time Time Current
Dload Upload Total Spent Left Speed
100 528 0 528 0 0 853 0 --:--:-- --:--:-- --:--:-- 858
Exercice
curl -X POST https://httpbin.org/post
-H "Content-Type: application/json"
-H "X-Custom-Header: MonHeader"
-d '{"action": "test", "value": 42}'
-i
TP 3
async function fetchWithRetry(url, options = {}, maxRetries = 3) {
let attempt = 0;

while (attempt <= maxRetries) {
try {
const response = await fetch(url, options);

  // Si erreur serveur (5xx)
  if (response.status >= 500 && response.status < 600) {
    throw new Error("Server error: " + response.status);
  }

  // Succès
  return response;

} catch (error) {
  if (attempt === maxRetries) {
    throw error; // après plusieurs essais → erreur finale
  }

  // attendre 1 seconde
  await new Promise(resolve => setTimeout(resolve, 1000));

  attempt++;
}
}
}


1. no-cache vs no-store
no-cache : la ressource peut être stockée mais doit être revalidée avant utilisation.
no-store : la ressource n’est jamais stockée.
2. Pourquoi POST n’est pas idempotent ?

POST n’est pas idempotent car plusieurs requêtes identiques peuvent créer plusieurs ressources ou modifier l’état du serveur différemment.

3. Code 301

Le code 301 indique une redirection permanente vers une nouvelle URL. Le navigateur redirige automatiquement.

4. Rôle de Origin

Le header Origin indique le domaine source de la requête. Il est utilisé pour la sécurité (CORS, CSRF).

5. HttpOnly sur les cookies

HttpOnly empêche l’accès aux cookies via JavaScript, ce qui protège contre les attaques XSS.