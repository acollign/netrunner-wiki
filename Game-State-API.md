Users that create an API Key (in the Settings page) can access some information about the game state via an API. This can be used by Twitch extensions that show decklists.

Currently the only exposed endpoint is `/game/deck` which allows access to the decklist of a player with a valid API key *and* if the game is created with the `Allow API Access` checkbox selected.

Sample usage:
```
# Invalid API Key
curl -i https://jinteki.net/game/deck -H "X-JNET-API: not-a-real-key"
HTTP/1.1 404 Not Found
Date: Tue, 27 Apr 2021 13:13:42 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 36
Connection: keep-alive

{"message":"Unknown X-JNet-API key"}

# Valid API key, player is not in an active game or in a game without the `Allow API Access` setting
% curl -i https://jinteki.net/game/deck -H "X-JNET-API: 7da39fa1-e187-4fbe-ae2a-170914228f3c"
HTTP/1.1 403 Forbidden
Date: Tue, 27 Apr 2021 13:13:47 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 55
Connection: keep-alive

{"message":"No game for key or API Access not enabled"}

# Valid API key, player is in an active game with the `Allow API Access` setting, no deck currently selected (ie. in the game lobby)
% curl -i https://jinteki.net/game/deck -H "X-JNET-API: 7da39fa1-e187-4fbe-ae2a-170914228f3c"
HTTP/1.1 204 No Content
Date: Tue, 27 Apr 2021 13:14:20 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 30
Connection: keep-alive

# Valid API key, player is in an active game with the `Allow API Access` setting, has a deck currently selected (in the game lobby or a running game)
% curl -i https://jinteki.net/game/deck -H "X-JNET-API: 7da39fa1-e187-4fbe-ae2a-170914228f3c"
HTTP/1.1 200 OK
Date: Tue, 27 Apr 2021 13:14:42 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1999
Connection: keep-alive

{"name":"[System Gateway] Corp Starter Deck","identity":{"title":"The Syndicate: Profit over Principle","image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30077.png"},"cards":[{"title":"Manegarm Skunkworks","qty":1,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30042.png"},{"title":"Urtica Cipher","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30045.png"},{"title":"Diviner","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30046.png"},{"title":"Send a Message","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30069.png"},{"title":"Superconducting Hub","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30070.png"},{"title":"Seamless Launch","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30040.png"},{"title":"Karunā","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30047.png"},{"title":"Government Subsidy","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30064.png"},{"title":"Brân 1.0","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30039.png"},{"title":"Palisade","qty":3,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30072.png"},{"title":"Tithe","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30073.png"},{"title":"Whitespace","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30074.png"},{"title":"Nico Campaign","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30037.png"},{"title":"Regolith Mining License","qty":2,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30071.png"},{"title":"Offworld Office","qty":3,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30067.png"},{"title":"Hedge Fund","qty":3,"image":"http://jinteki.net/img/cards/overrides/sg/en/default/stock/30075.png"}]}
```

