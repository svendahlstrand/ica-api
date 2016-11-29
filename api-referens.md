# API-referens

Dokumentationen följer en konvention för att visa frågorna till och svaren från
API:et.

* `GET /api/login` HTTP-metod och URL:en till en resurs
* `> Authorization: Basic ODYwNzE5NTkxMTo1NjM=` Request header
* `< 200` HTTP-statuskod
* `< AuthenticationTicket: 225DC3[..]` Response header

Response body:

```json
{
  "FirstName": "John",
  "LastName": "Doe",
  "Ttl": 1200
}
```

## Åtkomst

Du kommer åt API:et över HTTP och HTTPS på domänen `api.ica.se`. All data skickas
och tas emot som JSON. För de flesta anrop behöver tala om vem du är med hjälp
av en `AuthenticationTicket`.

## /api/login

Används för att få tag i en `AuthenticationTicket` som behövs för att tala om
vem du är när du gör anrop till servern.

Du använder HTTP Basic authentication med användarnamn och lösenord som du
[skaffar hos ICA](https://github.com/svendahlstrand/ica-api#skaffa-anvndarnamn-och-lsenord).

```
GET /api/login
> Authorization: Basic ODYwNzE4NTlxMSo1NjM=
< 200
< AuthenticationTicket: 225DC3[..]
< SessionTicket: 8EEA5E[..]
< LogoutKey: 7f9570[..]
```
```json
{
  "FirstName": "John",
  "LastName": "Doe",
  "Ttl": 1200
}
```

## /api/user/minasidor

Ta reda på information som rör ditt konto, saldo och rabatt.

```
GET /api/user/minasidor
> AuthenticationTicket: 225DC3[..]
< 200
```
```json
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
```

## /api/user/stores

De butiker som du oftast besöker.

```
GET /api/user/stores
> AuthenticationTicket: 225DC3[..]
< 200
```
```json
{
  "FavoriteStores": [1, 2, 3]
}
```

## /api/stores/1

Information om en butik.

```
GET /api/stores/1
> AuthenticationTicket: 225DC3[..]
< 200
```
```json
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
```

## /api/offers?Stores=XXXX

Listar dina erbjudanden och kuponger. Det går att skicka in flera butiks id. Separera då med komma.

```
GET /api/offers?Stores=1595
> AuthenticationTicket: 225DC3[..]
< 200
```
```json
{
    "Offers": [
        {
            "OfferId": "1000222315",
            "StoreId": 1595,
            "StoreIds": [
                1595
            ],
            "ArticleGroupId": 9,
            "OfferType": "handelsestyrd_ekupong",
            "ImageUrl": "http://extbild.ica.se/PictureWeb/80/54/14_1000222315.jpg",
            "PriceComparison": null,
            "SizeOrQuantity": "AXA , 375-500 g",
            "ProductName": "Havreflakes/Apple&Cinnamon",
            "OfferTypeTitle": "Kupong från Maxi ICA Stormarknad",
            "Disclaimer": "Max 1 st. Gäller 18/4-27/10. Lokalt kan avvikelser i sortiment förekomma.",
            "OfferCondition": "50% Lägre pris",
            "LoadedOnCard": true,
            "OfferUsed": true,
            "Expired": false,
            "Articles": [
                {
                    "EanId": "7310130002663",
                    "ArticleDescription": "Havreflakes/Apple&Cinnamon"
                },
                {
                    "EanId": "7310130003578",
                    "ArticleDescription": "Havreflakes/Apple&Cinnamon"
                }
            ]
        },
        {
            ...
        }
    ]
}
```

## /api/user/shoppinglists

Lista över inköpslistor

```
GET /api/user/shoppinglists
> AuthenticationTicket: [...]
< 200
```
```json
{
    "ShoppingLists": [
        {
            "Id": 651004,
            "Title": "Inköpslistan"
        }
    ]
}
```

## /api/user/shoppinglist/XXXXXX

Innehållet i en inköpslista. Ersätt XXXXXX med inköpslistans Id

```
GET /api/user/shoppinglists/XXXXXX
> AuthenticationTicket: [..]
< 200
```
```json
{
    "Id": 651004,
    "Title": "Inköpslistan",
    "CommentText": "",
    "SortingStore": 1595,
    "Rows": [
        {
            "RowId": 20083774,
            "ProductName": "Broccoli",
            "Quantity": 250,
            "SourceId": -1,
            "IsStrikedOver": true,
            "OfferId": null,
            "Unit": "g",
            "RecipeId": 716262,
            "InternalOrder": 3,
            "ArticleGroupId": 4
        },
        ...
    ]
}
```

## /api/recipes/categories/general

Listar namn på recept kategorier.

```
GET /api/recipes/categories/general
< 200
```
```json
{
    "Categories": [
        {
            "Id": 7,
            "Title": "Billiga Veckan"
        },
        {
            "Id": 36,
            "Title": "Grillrecept"
        },
        {
            "Id": 37,
            "Title": "Sallader"
        },
        {
            "Id": 24,
            "Title": "Grönare middagar"
        },
        {
            "Id": 19,
            "Title": "Vegetariska recept"
        },
        {
            "Id": 39,
            "Title": "Jordgubbar"
        },
        {
            "Id": 11,
            "Title": "Soppor"
        },
        {
            "Id": 21,
            "Title": "Fisk"
        },
        {
            "Id": 10,
            "Title": "Kyckling"
        },
        {
            "Id": 12,
            "Title": "Paj"
        },
        {
            "Id": 13,
            "Title": "Lasagne"
        },
        {
            "Id": 35,
            "Title": "Bakverk"
        },
        {
            "Id": 31,
            "Title": "Frukost"
        },
        {
            "Id": 25,
            "Title": "Smoothies"
        },
        {
            "Id": 9,
            "Title": "Tårta"
        },
        {
            "Id": 3,
            "Title": "Tidningen Buffé"
        },
        {
            "Id": 14,
            "Title": "Grytor"
        }
    ]
}
```

## /api/recipes/categories/general/X?PageNumber=X&RecordsPerPage=X

Recept i kategori.

```
GET /api/recipes/categories/general/7?PageNumber=1&RecordsPerPage=50
< 200
```
```json
{
    "CategoryName": "Billiga Veckan",
    "NumberOfPages": 15,
    "Recipes": [
        {
            "Id": 716500,
            "ImageId": 77140,
            "Title": "Färskpotatissallad med cajunkorv"
        },
        {
            "Id": 716501,
            "ImageId": 77142,
            "Title": "Kycklingpanna med timjan"
        },
        {
            ...
        }
    ]
}

## /api/recipes/recipe/XXXXXX

Information om ett recept.

GET /api/recipes/recipe/713666
< 200
{
    "Id": 713666,
    "Title": "Pastagratäng med kryddiga korvar",
    "ImageId": 35482,
    "YouTubeId": null,
    "IngredientGroups": [
        {
            "GroupName": "Ingredienser",
            "Ingredients": [
                {
                    "Text": "4 port pastaskruvar",
                    "IngredientId": 11210,
                    "Quantity": 4,
                    "Unit": null,
                    "Ingredient": "port pastaskruvar"
                },
                {
                    ...
                }
            ]
        },
        {
            ...
        }
    ],
    "PreambleHTML": "Recept på enkel och matig pastagratäng med kryddiga korvar som tillagas i ugn.",
    "CurrentUsersRating": null,
    "AverageRating": 3,
    "Difficulty": "Medel",
    "CookingTime": "Under 60 min",
    "Portions": 4
}
```
## /api/recipes/XXXXXX/rating

Betyg från användare

```
    GET /api/recipes/716405/rating
    > AuthenticationTicket: [...]
    < 200
```
```json
{
    "CurrentUserRating": 0
}
```
