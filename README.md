# ICA inofficiellt API

ICA har en [smart app](http://www.ica.se/smarta-mattjanster/icas-appar/ica-handla/)
som låter dig se ditt saldo, dina erbjudanden, göra inköpslistor med mera. Appen
är backad av ett API som jag har börjat dokumentera här. Börja med att följa
[kom igång-guiden](https://github.com/svendahlstrand/ica-api#kom-igng-hmta-information-om-ditt-konto)
och ta sedan en titt på [API-referensen](https://github.com/svendahlstrand/ica-api/blob/master/api-referens.md).

Dokumentationen är långt ifrån komplett och du får gärna hjälpa till, skicka bara
en Pull Request.

## Kom igång: hämta information om ditt konto

Här visar jag hur du enkelt kommer igång genom att hämta information om ditt
konto, saldo och dina rabatter.

### Skaffa användarnamn och lösenord

Du behöver ett användarnamn (ditt personnummer) och det lösenord som skickas
ut på ditt bonusbesked med tidningen Buffé varje månad. Orkar du inte vänta en
månad? [Kontakta ICA](http://www.ica.se/kundtjanst/fraga-ica) så skickar de
lösenordet med post.

### Använd din favorit-klient

När du testar API:et är det smidigt att ha en REST-klient. Jag rekommenderar:

* [Postman](http://www.getpostman.com) (Chrome-app)
* [rest-client](https://code.google.com/p/rest-client) (Java-baserad)
* [curl](http://curl.haxx.se) (För terminal-nörden)

I denna guide använder jag `curl` för att kunna visa exempel på ett enkelt sätt.

### Bas-URL

Du kommer åt API:et över HTTPS på domänen `handla.api.ica.se`.

### Skaffa en AuthenticationTicket

Först och främst behövs en `AuthenticationTicket` som används för att tala om
vem du är. Den får du genom ett `GET`-anrop till `/api/login`. Skicka med
använarnamn och lösenord med hjälp av HTTP Basic authentication.

```bash
$ curl -i -u 7001011234:567 https://handla.api.ica.se/api/login/
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
Expires: -1
Server: Microsoft-IIS/7.5
AuthenticationTicket: 8D66EA[..]
SessionTicket: 4F1E80[..]
LogoutKey: 59068e[..]
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
Date: Sun, 07 Apr 2013 18:44:24 GMT
Content-Length: 55
{"FirstName":"John","LastName":"Doe","Ttl":1200}
```

Bland svarets response headers hittar du din `AuthenticationTicket`. Spar den
till nästföljande anrop.

### Ställ en fråga om ditt konto

Nu ha du allt som behövs för att ställa frågor till API:et. Prova ett `GET`-anrop
till `/api/user/cardaccounts` och skicka med din `AuthenticationTicket` som en
request header.

```bash
$ curl \
  -H 'AuthenticationTicket: 8D66EA[..]' \
  https://handla.api.ica.se/api/user/cardaccounts/
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: application/json; charset=utf-8
Expires: -1
Instance: ABCVM4242A
Date: Mon, 15 Jun 2020 09:07:19 GMT
Content-Length: 166

{"CustomerNumber":123456789,"Cards":[{"Accounts":[],"CardTypeDescription":"ICA Kundkort utan betala","CardTypeCode":"90","MaskedCardNumber":"1234","Selected":false}]}
```

Svaret får du i JSON. Här ser du till exempel de kundkort du har registrerade på din användare.

### Nästa steg

Grattis, nu är du klar med guiden! Titta gärna på [API-referensen](https://github.com/svendahlstrand/ica-api/blob/master/api-referens.md)
för att komma vidare. Dokumentationen är under uppbyggnad och du får gärna
hjälpa till.
