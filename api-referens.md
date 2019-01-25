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

## /api/user/cardaccounts

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

## /api/user/minbonustransaction

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

## /api/user/recipes

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

## /api/stores/?LastSyncDate={timestamp}

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

## /api/stores/search?Filters&Phrase={phrase}

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

Lista över inköpslistor.

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

## /api/user/shoppinglists/\<ShoppinglistId\>/sync

Lägg till, ta bort och ändra i en inköpslista, sortera inköpslistan utefter butik. Ersätt \<ShoppinglistId\> med inköpslistans Id. Fyll i Parametern "SortingStore" med butikens id för att sortera listan efter ordningen i butiken.

###### Request
```
POST /api/user/shoppinglists/\<ShoppinglistId\>/sync
> AuthenticationTicket: [..]
> Content-Type: application/json
```` 
 ```json
{
    "ChangedRows": [
        {
            "_id": 5010,
            "ArticleGroupId": 12,
            "ArticleGroupIdExtended": 12,
            "ArticleId": 0,
            "Id": 0,
            "InternalOrder": 11,
            "IsStrikedOver": true,
            "OfferId": 0,
            "ProductName": "Test",
            "Quantity": 0.0,
            "RecipeId": 0,
            "RowId": 132946775,
            "SourceId": -1
        }
    ],
    "DeletedRows": [
        1234156
    ],
    "CreatedRows": [
        {
            "_id": 5241,
            "ArticleGroupId": 4,
            "ArticleGroupIdExtended": 4,
            "ArticleId": 0,
            "Id": 0,
            "FormatCategoryKvantum": "3310",
            "FormatCategoryMaxi": "3310",
            "FormatCategoryNara": "3310",
            "FormatCategorySuperMarket": "3310",
            "InternalOrder": 11,
            "IsStrikedOver": false,
            "OfferId": 0,
            "ProductName": "Frukt",
            "Quantity": 1.0,
            "RecipeId": 0,
            "RowId": 0,
            "SourceId": 696492,
            "Unit": "förp"
        }
    ],
    "ChangedShoppingListProperties": {
        "SortingStore": 0,
        "Title": "Att handla, 22 jan 2019"
    }
}
```
###### Response
```
< 200
< Content-Type: application/json
```` 
```json
{
    "Id": <ShoppinglistId>,
    "Title": "Att handla, 22 jan 2019",
    "CommentText": null,
    "SortingStore": 0,
    "Rows": [
        {
            "RowId": 132946775,
            "ProductName": "Test",
            "Quantity": 0,
            "SourceId": 1006427,
            "IsStrikedOver": true,
            "OfferId": null,
            "Unit": null,
            "RecipeId": null,
            "InternalOrder": 11,
            "ArticleGroupId": 12,
            "ArticleGroupIdExtended": null,
            "FormatCategoryMaxi": null,
            "FormatCategoryKvantum": null,
            "FormatCategorySuperMarket": null,
            "FormatCategoryNara": null,
            "ProductEan": null
        }
    ]
}
```

## /api/user/commonarticles/ 

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

## /api/recipes/categories/general/{categoryId}?RecordsPerPage=x&PageNumber=x&Include=ImageId,Title,CookingTime,AverageRating,OfferCount,IngredientCount

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


## /api/recipes/search?RecordsPerPage=x&PageNumber=x&Phrase={phrase}&Include=ImageId,Title,CookingTime,AverageRating,OfferCount,IngredientCount

Sök efter recept. Ersätt {phrase} med sökord i recept du söker efter.

```
GET /api/recipes/search?RecordsPerPage=40&PageNumber=1&Phrase=pizza&Include=ImageId,Title,CookingTime,AverageRating,OfferCount,IngredientCount
> AuthenticationTicket: [...]
< 200
```
```
{
  "CategoryName": null,
  "NumberOfPages": 4,
  "Recipes": [
    {
      "Id": 714168,
      "ImageId": 42438,
      "Title": "Pizza",
      "PreambleHTML": null,
      "Difficulty": null,
      "CookingTime": "Under 30 minuter",
      "AverageRating": "3.0",
      "IngredientCount": 7,
      "OfferCount": 0
    },
    ...
	],
	"TotalNumberOfRecipes": 142
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
```

## /api/recipes/search/filters

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

## /api/recipes/recipe/XXXXXX

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
## /api/recipes/XXXXXX/rating

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

## /api/upclookup

Efterfråga produktinformation för en EAN-kod. Informationen är mycket kortfattad och med varierande precision, men kan räcka för sökning, inköpslistor etc. Samma funktion används av ICA-appen vid streckkodsscanning i inköpslistan.

Ingen inloggning behövs.

Det går att fråga om flera produkter på en gång, a la "7310390001383,7310751163903". Items innehåller då ett element per produkt.

```
GET /api/upclookup?upc=7313350007203
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

## /api/status

###### Request
```
GET /api/status
> AuthenticationTicket: [..]
> User-Agent: ICA Handla Android 2.4.0 (185) 9
```

###### Response
```
< 200
< Content-Type: application/json
```` 

 ```json
{
    "VersionCheck": {
        "AlertTitle": null,
        "AlertDescription": null,
        "AlertButtonTitle": null,
        "AlertLink": null,
        "MustUpdate": false,
        "UpdatedVersion": 0,
        "CustomerRole": -9
    },
    "Customer": {
        "Role": 7
    }
}
```
