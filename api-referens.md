# API-referens

Dokumentationen följer en konvention för att visa frågorna till och svaren från
API:et.

* `GET /api/login` HTTP-metod och URL:en till en resurs
* `> Authorization: Basic ODYwNzE5NTkxMTo1NjM=` Request header
* `< 200` HTTP-statuskod
* `< AuthenticationTicket: 225DC3[..]` Response header

Response body:

    {
      "FirstName": "John",
      "LastName": "Doe",
      "Ttl": 1200
    }

## Åtkomst

Du kommer åt API:et över HTTP och HTTPS på domänen `api.ica.se`. All data skickas
och tas emot som JSON. För de flesta anrop behöver tala om vem du är med hjälp
av en `AuthenticationTicket`.

## /api/login

Används för att få tag i en `AuthenticationTicket` som behövs för att tala om
vem du är när du gör anrop till servern.

Du använder HTTP Basic authentication med användarnamn och lösenord som du
[skaffar hos ICA](https://github.com/svendahlstrand/ica-api#skaffa-anvndarnamn-och-lsenord).

    GET /api/login
    > Authorization: Basic ODYwNzE4NTlxMSo1NjM=
    < 200
    < AuthenticationTicket: 225DC3[..]
    < SessionTicket: 8EEA5E[..]
    < LogoutKey: 7f9570[..]
    {
      "FirstName": "John",
      "LastName": "Doe",
      "Ttl": 1200
    }

## /api/user/minasidor

Ta reda på information som rör ditt konto, saldo och rabatt.

    GET /api/user/minasidor
    > AuthenticationTicket: 225DC3[..]
    < 200
    {
      "Saldo": 0.0,
      "YearlyTotalPurchased": 10000.00,
      "AcquiredDiscount": 100.00,
      "AmountSinceLastBonusCheck": 0.0,
      "AmountLeftUntilNextBonusCheck": 2000.0,
      "NextBonusCheckValue": 0.0,
      "AcquiredBonus": 150.00,
      "IcaBankUrl": "http://mobil.icabanken.se",
      "AccountNumber": "123 456 789",
      "AvailableAmount": 0.00,
      "CreditLimit": 0.0
    }

## /api/user/stores

De butiker som du oftast besöker.

    GET /api/user/stores
    > AuthenticationTicket: 225DC3[..]
    < 200
    {
      "FavoriteStores": [1, 2, 3]
    }

## /api/stores/1

Information om en butik.

    GET /api/stores/1
    > AuthenticationTicket: 225DC3[..]
    < 200
    {
        "Id": 1,
        "MarketingName": "ICA Nära Lill-ICA",
        "Address": {
            "Street": "Dalgatan 5",
            "Zip": "933 33",
            "City": "ARVIDSJAUR"
        },
        "Phone": "0960-211 89",
        "Coordinates": {
            "Latitude": 65.59017,
            "Longitude": 19.19496
        },
        "WebURL": "http://www.ica.se/butiker/nara/arvidsjaur/ica-nara-lill-ica-1/start/",
        "FacebookUrl": null,
        "FilterItems": [11, 7],
        "ProfileId": "04",
        "OpeningHours": {
            "Today": "12-20",
            "RegularHours": [{
                    "Title": "Mån-Fre",
                    "Hours": "09-20"
                }, {
                    "Title": "Lördag",
                    "Hours": "09-18"
                }, {
                    "Title": "Söndag",
                    "Hours": "12-20"
                }
            ],
            "SpecialHours": [],
            "OtherOpeningHours": null
        }
    }
