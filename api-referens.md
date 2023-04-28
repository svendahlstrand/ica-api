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

Du kommer åt API:et över HTTPS på domänen `handla.api.ica.se`. All data skickas
och tas emot som JSON. För de flesta anrop behöver tala om vem du är med hjälp
av en <code><a href="#autentisering">AuthenticationTicket</a></code>.

# Anrop

## Innehållsförteckning

- [Autentisering](#autentisering)
  - `GET` /api/login
- [Kort](#kort)
  - `GET` /api/user/cardaccounts
- [Min Bonus](#min-bonus)
  - `GET` /api/user/minbonustransaction
- [Affärer](#affärer)
  - `GET` /api/user/stores
  - `GET` /api/stores/1
  - `GET` /api/stores/?LastSyncDate={timestamp}
  - `GET` /api/stores/search?Filters&Phrase={phrase}
- [Erbjudanden](#erbjudanden)
  - `GET` /api/offers?Stores=XXXX
- [Inköpslistor](#inköpslistor)
- [Artikelgrupper](#artikelgrupper)
  - `GET` /api/articles/articlegroups?lastsyncdate={timestamp}
- [Vanliga artiklar](#vanliga-artiklar)
  - `GET` /api/user/commonarticles/ + Request + Response
- [Recept](#recept)
  - `GET` /api/user/recipes
  - `GET` /api/recipes/searchwithfilters?phrase={phrase}&recordsPerPage=x&pageNumber=x&sorting=x
  - `GET` /api/recipes/search/filters
  - `GET` /api/recipes/recipe/XXXXXX
  - `GET` /api/recipes/XXXXXX/rating
  - `GET` /api/recipes/random?numberofrecipes=x
- [Receptkategorier](#receptkategorier)
  - `GET` /api/recipes/categories/general
  - `GET` /api/recipes/categories/general/{categoryId}?RecordsPerPage=x&PageNumber=x&Include=ImageId,Title,CookingTime,AverageRating,OfferCount,- IngredientCount
  - `GET` /api/recipes/categories/general/X?PageNumber=X&RecordsPerPage=X
- [Strekkodssökning](#strekkodssökning)
  - `GET` /api/upclookup

## Autentisering
### `GET` /api/login

Används för att få tag i en `AuthenticationTicket` som behövs för att tala om
vem du är när du gör anrop till servern.

Du använder HTTP Basic authentication med användarnamn och lösenord som du
[skaffar hos ICA](https://github.com/svendahlstrand/ica-api#skaffa-användarnamn-och-lösenord).

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

## Kort

### `GET` /api/user/cardaccounts

Visa vilka kort som är registrerade för den inloggade användaren.

```
GET /api/user/cardaccounts
> AuthenticationTicket: 225DC3[..]
< 200
```
```json
{
  "CustomerNumber": 123456,
  "Cards": [
    {
      "Accounts": null,
      "CardTypeDescription": "ICA KUNDKORT UTAN BETALA",
      "CardTypeCode": "90",
      "MaskedCardNumber": "0123",
      "Selected": false
    }
  ]
}
```

## Min Bonus
### `GET` /api/user/minbonustransaction

Visar tidigare köp du gjort i ICA-butiker.

```
GET /api/user/minbonustransaction
> AuthenticationTicket: 225DC3[..]
< 200
```
```json
{
  "TransactionSummaryByMonth": [
    {
      "Year": "2019",
      "Month": "6",
      "YearMonthAsDateTime": "2019-06-01T00:00:00",
      "TransactionForAMonth": [
        {
          "TransactionDate": "20190610",
          "MarketingName": "ICA Supermarket Ankeborg",
          "TotalDiscount": 0,
          "TransactionValue": 46.5
        }
      ]
    },
    {
      "Year": "2019",
      "Month": "5",
      "YearMonthAsDateTime": "2019-05-01T00:00:00",
      "TransactionForAMonth": [
        {
          "TransactionDate": "20190514",
          "MarketingName": "ICA Supermarket Ankeborg",
          "TotalDiscount": 0,
          "TransactionValue": 145.44
        }
      ]
    },
    ...
  ]
}
```

## Affärer

### `GET` /api/user/stores

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

### `GET` /api/stores/1

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

### `GET` /api/stores/?LastSyncDate={timestamp}

Visa ICA butiker. Ersätt {timestamp} med en tidsstämpel med formatet YYYY-MM-DD.

**OBS! Tung request som returnerar mycket data!**

```
GET /api/stores/?LastSyncDate=2014-12-01
> AuthenticationTicket: 225DC3[..]
< 200
```
```json
{
  "Stores": [
    {
      "Id": 1,
      "StoreName": "ICA Nära Lill-ICA",
      "Address": {
        "Street": "Dalgatan 5",
        "Zip": "933 33",
        "City": "ARVIDSJAUR"
      },
      "Coordinates": {
        "Latitude": 65.59017,
        "Longitude": 19.19496
      },
      "IsActive": true,
      "ProfileId": "04",
      "ShoppingRound": "12,4,3,6,10,5,9,11,7,8",
      "ShoppingRoundType": 1,
      "DefaultShoppingRound": true,
      "StoreMapActive": false
    },
    ...
    ]
}
```

### `GET` /api/stores/search?Filters&Phrase={phrase}

Sök efter ICA butiker, ersätt {phrase} med ett sökord.

```
GET /api/stores/search?Filters&Phrase=lidköping
> AuthenticationTicket: 225DC3[..]
< 200
```
```json
{
  "Stores": [
    2043,
    2050,
    2044,
    2046,
    2014
  ]
}
```

## Erbjudanden

### `GET` /api/offers?Stores=XXXX

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

## Inköpslistor

### `GET` /api/user/offlineshoppinglists

Hämta alla inköpslistor.

###### Request
```
GET /api/user/offlineshoppinglists
> AuthenticationTicket: [...]
< 200
```

###### Response
```json
{
  "ShoppingLists": [
    {
      "Id": 1,
      "Title": "Handla 08 januari",
      "CommentText": "",
      "SortingStore": 0,
      "Rows": [
        {
          "RowId": 1,
          "ProductName": "Wrap tortilla",
          "Quantity": 0.000,
          "SourceId": 1,
          "IsStrikedOver": false,
          "InternalOrder": 1,
          "ArticleGroupId": 9,
          "ArticleGroupIdExtended": 9,
          "LatestChange": "2021-01-08T16:25:01Z",
          "OfflineId": "xxxxxx",
          "IsSmartItem": false
        }
      ],
      "LatestChange": "2021-01-08T16:25:05Z",
      "OfflineId": "xxxxxx",
      "IsPrivate": false,
      "IsSmartList": false
    }
  ]
}
```

### `GET` /api/user/offlineshoppinglists/\<OfflineId\>

Hämta en specifik inköpslista. Lägg märke till att `OfflineId` måste användas i URLen och inte `Id`.

###### Request
```
GET /api/user/offlineshoppinglists/\<OfflineId\>
> AuthenticationTicket: [...]
< 200
```

###### Response
```json
{
  "Id": 1,
  "Title": "Handla 08 januari",
  "CommentText": "",
  "SortingStore": 0,
  "Rows": [
    {
      "RowId": 1,
      "ProductName": "Wrap tortilla",
      "Quantity": 0.000,
      "SourceId": 1,
      "IsStrikedOver": false,
      "InternalOrder": 1,
      "ArticleGroupId": 9,
      "ArticleGroupIdExtended": 9,
      "LatestChange": "2021-01-08T16:25:01Z",
      "OfflineId": "xxxxxx",
      "IsSmartItem": false
    }
  ],
  "LatestChange": "2021-01-08T16:25:05Z",
  "OfflineId": "xxxxxx",
  "IsPrivate": false,
  "IsSmartList": false
}
```

### `POST` /api/user/offlineshoppinglists/\<OfflineId\>/sync

Synka den lokala shoppinglistan till servern. Produkter med `"SourceId": -1` är produkter som ännu inte finns på servern.

###### Request
```
POST /api/user/offlineshoppinglists/\<OfflineId\>/sync
> AuthenticationTicket: [...]
< 200
```

```json
{
  "Id": 1,
  "Title": "Handla 08 januari",
  "CommentText": "",
  "SortingStore": 0,
  "Rows": [
    {
      "RowId": 1,
      "ProductName": "Wrap tortilla",
      "Quantity": 0.000,
      "SourceId": 1,
      "IsStrikedOver": false,
      "InternalOrder": 2,
      "ArticleGroupId": 9,
      "ArticleGroupIdExtended": 9,
      "LatestChange": "2021-01-08T21:42:52Z",
      "OfflineId": "xxxxxx",
      "IsSmartItem": false
    },
    {
      "RowId": 1,
      "ProductName": "Banan",
      "Quantity": 0.000,
      "SourceId": -1,
      "IsStrikedOver": false,
      "InternalOrder": 3,
      "ArticleGroupId": 4,
      "ArticleGroupIdExtended": 4,
      "ProductEan": "",
      "LatestChange": "2021-01-08T21:42:52Z",
      "OfflineId": "xxxxxx",
      "IsSmartItem": false
    }
  ],
  "LatestChange": "2021-01-08T21:42:52Z",
  "OfflineId": "xxxxxx",
  "IsPrivate": false,
  "IsSmartList": false
}
```

###### Response
```json
{
  "Id": 1,
  "Title": "Handla 08 januari",
  "CommentText": "",
  "SortingStore": 0,
  "Rows": [
    {
      "RowId": 1,
      "ProductName": "Wrap tortilla",
      "Quantity": 0.000,
      "SourceId": 1,
      "IsStrikedOver": false,
      "InternalOrder": 2,
      "ArticleGroupId": 9,
      "ArticleGroupIdExtended": 9,
      "LatestChange": "2021-01-08T21:42:52Z",
      "OfflineId": "xxxxxx",
      "IsSmartItem": false
    },
    {
      "RowId": 1,
      "ProductName": "Banan",
      "Quantity": 0.000,
      "SourceId": 0,
      "IsStrikedOver": false,
      "InternalOrder": 3,
      "ArticleGroupId": 4,
      "ArticleGroupIdExtended": 4,
      "ProductEan": "",
      "LatestChange": "2021-01-08T21:42:52Z",
      "OfflineId": "xxxxxx",
      "IsSmartItem": false
    }
  ],
  "LatestChange": "2021-01-08T21:42:52Z",
  "OfflineId": "xxxxxx",
  "IsPrivate": false,
  "IsSmartList": false
}
```

### `POST` /api/user/offlineshoppinglists

Skapa en ny inköpslista.

###### Request
```
POST /api/user/offlineshoppinglists
> AuthenticationTicket: [...]
< 200
```

```json
{
  "OfflineId": "xxxxxx",
  "Title": "Min fina inköpslista",
  "CommentText": "",
  "SortingStore": 0,
  "Rows": [],
  "LatestChange": "2021-01-08T22:52:33Z"
}
```

###### Response
```json
{
  "Id": 1
}
```

### `DELETE` /api/user/offlineshoppinglists/<OfflineId>

Ta bort en inköpslista.

###### Request
```
DELETE /api/user/offlineshoppinglists/<OfflineId>
> AuthenticationTicket: [...]
< 200
```

###### Response
```
No content
```

## Artikelgrupper

### `GET` /api/articles/articlegroups?lastsyncdate={timestamp}

Hämta alla artikelgrupper sedan ett visst datum.

###### Request
```
GET/api/articles/articlegroups?lastsyncdate=2001-01-01
> AuthenticationTicket: [...]
< 200
```

###### Response
```json
{
    "ArticleGroups": [
        {
            "Id": 3,
            "Name": "Bröd, kex och bageri",
            "ParentId": 1,
            "LastSyncDate": "2015-09-18T08:45:55"
        },
        {
            "Id": 4,
            "Name": "Frukt & Grönt",
            "ParentId": 1,
            "LastSyncDate": "2015-06-02T15:03:20"
        },
        // [...]
    ]
}
```

## Vanliga Artiklar

### `GET` /api/user/commonarticles/

Hämtar en lista på frekvent använda varor

###### Request
```
GET /api/user/commonarticles/
> AuthenticationTicket: [..]
```

###### Response
```
< 200
< Content-Type: application/json
````

 ```json
{
    "CommonArticles": [
        {
            "Id": 7601571,
            "ProductName": "Bröd",
            "ArticleId": 10241,
            "ArticleGroupId": 3,
            "ArticleGroupIdExtended": 3,
            "FormatCategoryMaxi": "2217",
            "FormatCategoryKvantum": "2217",
            "FormatCategorySuperMarket": "2217",
            "FormatCategoryNara": "2217"
        }
    ]
}
```

## Recept

### `GET` /api/user/recipes

Ta fram användarens sparade recept.

```
GET /api/user/recipes
> AuthenticationTicket: 225DC3[..]
< 200
```
```json
{
  "RecipeIds": [
    720948,
    720947,
    721249
  ],
  "UserRecipes": [
    {
      "RecipeId": 720948,
      "CreationDate": "2016-12-01T14:05:42"
    },
    {
      "RecipeId": 720947,
      "CreationDate": "2016-12-01T14:05:42"
    },
    {
      "RecipeId": 721249,
      "CreationDate": "2016-12-01T14:05:42"
    }
  ]
}
```

### `GET` /api/recipes/searchwithfilters?phrase={phrase}&recordsPerPage=x&pageNumber=x&sorting=x

Sök efter recept. Ersätt {phrase} med sökord i recept du söker efter.

```
GET /api/recipes/searchwithfilters?recordsPerPage=40&pageNumber=1&phrase=pizza&sorting=0
> AuthenticationTicket: [...]
< 200
```
```
{
  "NumberOfPages": 4,
  "Recipes": [
    {
      "Id": 714168,
      "ImageId": 42438,
      "ImageUrl": ""
      "Title": "Pizza",
      "PreambleHTML": null,
      "CookingTime": "Under 30 minuter",
      "CookingTimeMinutes": 30,
      "AverageRating": "3.0",
      "CommentCount": 0,
      "AverageRating": "4.3"
      "IngredientCount": 7,
      "OfferCount": 0
    },
    ...
	],
	"TotalNumberOfRecipes": 142,
	"Msg": ""
}
```

### `GET` /api/recipes/search/filters

Lista receptfilter.

```
GET /api/recipes/search/filters
< 200
```
```json
{
  "FilterItems": [
    {
      "Id": 2,
      "ImageId": null,
      "SelectedImageId": null,
      "Name": "Förrätt"
    },
    {
      "Id": 3,
      "ImageId": null,
      "SelectedImageId": null,
      "Name": "Efterrätt"
    },
    {
      "Id": 4,
      "ImageId": null,
      "SelectedImageId": null,
      "Name": "Huvudrätt"
    },
    ...
    ]
}
```

### `GET` /api/recipes/recipe/XXXXXX

Information om ett recept.

```
GET /api/recipes/recipe/713666
< 200
```
```json
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
### `GET` /api/recipes/XXXXXX/rating

Betyg från användare.

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

### `GET` /api/recipes/random?numberofrecipes=x

Hämtar slumpvist utvalda recept.

```
GET /api/recipes/random?numberofrecipes=1
< 200
```
```json
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

## Receptkategorier

### `GET` /api/recipes/categories/general

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

### `GET` /api/recipes/categories/general/{categoryId}?RecordsPerPage=x&PageNumber=x&Include=ImageId,Title,CookingTime,AverageRating,OfferCount,IngredientCount

Visa recept i kategori. Ersätt {categoryId} med id från en av kategorierna från endpoint ovan.

```
GET /api/recipes/categories/general/88?RecordsPerPage=30&PageNumber=1&Include=ImageId,Title,CookingTime,AverageRating,OfferCount,IngredientCount
> AuthenticationTicket: [...]
< 200
```
```
{
  "CategoryName": "Lax",
  "NumberOfPages": 31,
  "Recipes": [
    {
      "Id": 688127,
      "ImageId": 79179,
      "Title": "Ugnsbakad lax med limecrème fraiche",
      "PreambleHTML": null,
      "Difficulty": null,
      "CookingTime": "Under 30 minuter",
      "AverageRating": "4",
      "IngredientCount": 12,
      "OfferCount": 1
    },
    ...
    ],
    "TotalNumberOfRecipes": 915
}
```

### `GET` /api/recipes/categories/general/X?PageNumber=X&RecordsPerPage=X

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
```

## Strekkodssökning

### `GET` /api/upclookup

Efterfråga artikelinformation för en EAN-kod. Informationen är mycket kortfattad men kan räcka för sökning, inköpslistor och så vidare.

Det går att fråga om flera artiklar på en gång, a la "7310390001383,7310751163903". Items innehåller då ett element per artikel.

```
GET /api/upclookup?upc=7313350007203
> AuthenticationTicket: [...]
< 200
```
```json
{
    "Items": [
        {
            "Upc": "7313350007203",
            "ItemDescription": "Knäcke runda Original 280g Polarbröd",
            "ArticleGroup": 3,
            "ArticleGroupExtended": 3,
            "FormatCategoryMaxi": "1270.10",
            "FormatCategoryKvantum": "1270.10",
            "FormatCategorySuperMarket": "1270.10",
            "FormatCategoryNara": "1270.10",
            "ProductGroup": {
                "Category": "Bröd, kex & bageri",
                "SubCategory": "Knäckebröd",
                "Suggestion": "knäcke"
            }
        }
    ]
}
```
