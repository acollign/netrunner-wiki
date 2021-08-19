_Note:_ In the first version, a user's decklist was available from the `/deck` endpoint. It is now available at `/decklist` and `/deck` returns the card codes that have yet to be drawn from the deck.

Users that create an API Key (in the Settings page) can access some information about the game state via an API. This can be used by Twitch extensions that show decklists.

There are a few endpoints available:
  - `/game/decklist` which allows access to the decklist of a player with a valid API key *and* if the game is created with the `Allow API Access` checkbox selected
  - `/game/deck` which shows the card code of any cards yet to be drawn
  - `/game/hand` which shows the card code of cards in the user's hand
  - `/game/discard` which shows the card code cards in the user's discard pile
  - `/game/log` which shows the text of the game log

Sample usage:
```
# Invalid API Key
curl -i https://jinteki.net/game/decklist -H "X-JNET-API: not-a-real-key"
HTTP/1.1 404 Not Found
Date: Tue, 27 Apr 2021 13:13:42 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 36
Connection: keep-alive

{"message":"Unknown X-JNet-API key"}

# Valid API key, player is not in an active game or in a game without the `Allow API Access` setting
% curl -i https://jinteki.net/game/decklist -H "X-JNET-API: 7da39fa1-e187-4fbe-ae2a-170914228f3c"
HTTP/1.1 403 Forbidden
Date: Tue, 27 Apr 2021 13:13:47 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 55
Connection: keep-alive

{"message":"No game for key or API Access not enabled"}

# Valid API key, player is in an active game with the `Allow API Access` setting, no deck currently selected (ie. in the game lobby)
% curl -i https://jinteki.net/game/decklist -H "X-JNET-API: 7da39fa1-e187-4fbe-ae2a-170914228f3c"
HTTP/1.1 204 No Content
Date: Tue, 27 Apr 2021 13:14:20 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 30
Connection: keep-alive

# Valid API key, player is in an active game with the `Allow API Access` setting, has a deck currently selected (in the game lobby or a running game)
% curl -i https://jinteki.net/game/decklist -H "X-JNET-API: 7da39fa1-e187-4fbe-ae2a-170914228f3c"
HTTP/1.1 200 OK
Cache-Control: no-store
Content-Type: application/json; charset=utf-8
Content-Length: 11471
Server: http-kit
Date: Thu, 19 Aug 2021 17:30:10 GMT

{"name":"Lockdown ACME (2nd @ Lockdown #14)","identity":{"title":"Acme Consulting: The Truth You Need","details":{"faction":"NBN","cycle_code":"reign-and-reverie","setname":"Reign and Reverie","influencelimit":15,"number":42,"type":"Identity","title":"Acme Consulting: The Truth You Need","uniqueness":false,"code":"22042","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/22042.png","deck-limit":1,"set_code":"rar","subtype":"Subsidiary","minimumdecksize":45,"text":"The Runner is considered to have 1 additional tag (even if they have 0) during encounters with the outermost piece of ice protecting any server.","subtypes":["Subsidiary"]}},"cards":[{"qty":2,"title":"Sprint","details":{"faction":"Haas-Bioroid","cycle_code":"system-gateway","factioncost":1,"setname":"System Gateway","number":41,"type":"Operation","title":"Sprint","uniqueness":false,"code":"30041","side":"Corp","image":"http://localhost:1042/img/cards/overrides/sg/en/default/stock/30041.png","cost":0,"deck-limit":3,"set_code":"sg","text":"Draw 3 cards. Shuffle 2 cards from HQ into R&D.","subtypes":[]}},{"qty":3,"title":"Bellona","details":{"faction":"NBN","cycle_code":"ashes","setname":"Uprising","agendapoints":3,"number":114,"advancementcost":5,"type":"Agenda","title":"Bellona","uniqueness":false,"code":"26114","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/26114.png","deck-limit":3,"set_code":"ur","subtype":"Expansion","text":"As an additional cost to steal this agenda, the Runner must pay 5[credit].\nWhen you score this agenda, gain 5[credit].","subtypes":["Expansion"]}},{"qty":2,"title":"Government Subsidy","details":{"faction":"Weyland Consortium","cycle_code":"system-gateway","factioncost":1,"setname":"System Gateway","number":64,"type":"Operation","title":"Government Subsidy","uniqueness":false,"code":"30064","side":"Corp","image":"http://localhost:1042/img/cards/overrides/sg/en/default/stock/30064.png","cost":10,"deck-limit":3,"set_code":"sg","subtype":"Transaction","text":"Gain 15[credit].","subtypes":["Transaction"]}},{"qty":3,"title":"IP Block","details":{"faction":"NBN","cycle_code":"flashpoint","factioncost":1,"setname":"Martial Law","number":94,"type":"ICE","title":"IP Block","uniqueness":false,"strength":4,"code":"11094","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/11094.png","cost":2,"deck-limit":3,"set_code":"ml","subtype":"Barrier - Tracer","text":"When the Runner encounters IP Block, give him or her 1 tag if there is an installed <strong>AI</strong>.\n[subroutine] <trace>Trace 3</trace> If successful, give the Runner 1 tag.\n[subroutine] End the run if the Runner is tagged.","subtypes":["Barrier","Tracer"]}},{"qty":3,"title":"Predictive Planogram","details":{"faction":"NBN","cycle_code":"system-gateway","factioncost":1,"setname":"System Gateway","number":56,"type":"Operation","title":"Predictive Planogram","uniqueness":false,"code":"30056","side":"Corp","image":"http://localhost:1042/img/cards/overrides/sg/en/default/stock/30056.png","cost":0,"deck-limit":3,"set_code":"sg","subtype":"Transaction","text":"Resolve 1 of the following. If the Runner is tagged, you may resolve both instead.<ul><li>Gain 3[credit].</li><li>Draw 3 cards.</li></ul>","subtypes":["Transaction"]}},{"qty":3,"title":"Daily Quest","details":{"faction":"NBN","cycle_code":"ashes","factioncost":3,"setname":"Downfall","number":48,"type":"Asset","title":"Daily Quest","uniqueness":false,"code":"26048","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/26048.png","cost":1,"deck-limit":3,"trash":3,"set_code":"df","text":"Rez only during your action phase.\nWhenever the Runner makes a successful run on this server, they gain 2[credit].\nWhen your turn begins, gain 3[credit] if the Runner did not make any successful runs on this server during their last turn.","subtypes":[]}},{"qty":1,"title":"Subliminal Messaging","details":{"faction":"Neutral","cycle_code":"system-update-2021","factioncost":0,"setname":"System Update 2021","number":82,"type":"Operation","title":"Subliminal Messaging","uniqueness":false,"code":"31082","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/31082.png","cost":0,"deck-limit":3,"set_code":"su21","subtype":"Gray Ops","text":"Gain 1[credit].\nThe first time each turn you play a copy of Subliminal Messaging, gain [click].\nWhen your turn begins, if this card is in Archives and the Runner did not initiate any runs during their last turn, you may reveal this card and add it to HQ.","subtypes":["Gray Ops"]}},{"qty":3,"title":"Spin Doctor","details":{"faction":"NBN","cycle_code":"system-gateway","factioncost":1,"setname":"System Gateway","number":53,"type":"Asset","title":"Spin Doctor","uniqueness":true,"code":"30053","side":"Corp","image":"http://localhost:1042/img/cards/overrides/sg/en/default/stock/30053.png","cost":0,"deck-limit":3,"trash":2,"set_code":"sg","subtype":"Character","text":"When you rez this asset, draw 2 cards.\n<strong>Remove this asset from the game:</strong> Shuffle up to 2 cards from Archives into R&D.","subtypes":["Character"]}},{"qty":3,"title":"Thimblerig","details":{"faction":"Jinteki","cycle_code":"reign-and-reverie","factioncost":1,"setname":"Reign and Reverie","number":39,"type":"ICE","title":"Thimblerig","uniqueness":false,"strength":0,"code":"22039","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/22039.png","cost":2,"deck-limit":3,"set_code":"rar","subtype":"Code Gate","text":"When your turn begins or whenever the Runner passes Thimblerig, you may swap Thimblerig with another installed piece of ice.\n[subroutine] End the run.","subtypes":["Code Gate"]}},{"qty":3,"title":"F2P","details":{"faction":"NBN","cycle_code":"ashes","factioncost":2,"setname":"Uprising","number":115,"type":"ICE","title":"F2P","uniqueness":false,"strength":5,"code":"26115","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/26115.png","cost":4,"deck-limit":3,"set_code":"ur","subtype":"Sentry","text":"2[credit]: Break 1 subroutine on this ice. Only the Runner can use this ability, and only if they are not tagged.\n[subroutine] Add 1 installed Runner card to the grip.\n[subroutine] Give the Runner 1 tag.","subtypes":["Sentry"]}},{"qty":1,"title":"Tomorrow's Headline","details":{"faction":"NBN","cycle_code":"system-gateway","setname":"System Gateway","agendapoints":2,"number":52,"advancementcost":3,"type":"Agenda","title":"Tomorrow's Headline","uniqueness":false,"code":"30052","side":"Corp","image":"http://localhost:1042/img/cards/overrides/sg/en/default/stock/30052.png","deck-limit":1,"set_code":"sg","subtype":"Ambush","text":"When this agenda is scored or stolen, give the Runner 1 tag.\nLimit 1 per deck.","subtypes":["Ambush"]}},{"qty":2,"title":"Anoetic Void","details":{"faction":"Jinteki","cycle_code":"system-gateway","factioncost":4,"setname":"System Gateway","number":50,"type":"Upgrade","title":"Anoetic Void","uniqueness":true,"code":"30050","side":"Corp","image":"http://localhost:1042/img/cards/overrides/sg/en/default/stock/30050.png","cost":0,"deck-limit":3,"trash":1,"set_code":"sg","text":"Whenever the Runner approaches this server, you may pay 2[credit] and trash 2 cards from HQ. If you do, end the run.","subtypes":[]}},{"qty":3,"title":"NGO Front","details":{"faction":"Neutral","cycle_code":"kitara","factioncost":0,"setname":"Down the White Nile","number":39,"type":"Asset","title":"NGO Front","uniqueness":false,"code":"21039","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/21039.png","cost":0,"deck-limit":3,"trash":1,"set_code":"dtwn","text":"NGO Front can be advanced.\n[trash],<strong>1 hosted advancement token</strong>: Gain 5[credit].\n[trash],<strong>2 hosted advancement tokens</strong>: Gain 8[credit].","subtypes":[]}},{"qty":3,"title":"Data Ward","details":{"faction":"NBN","cycle_code":"flashpoint","factioncost":3,"setname":"Intervention","number":75,"type":"ICE","title":"Data Ward","uniqueness":false,"strength":8,"code":"11075","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/11075.png","cost":6,"deck-limit":3,"set_code":"in","subtype":"Barrier","text":"When the Runner encounters Data Ward, he or she must pay 3[credit] or take 1 tag.\n[subroutine] End the run if the Runner is tagged.\n[subroutine] End the run if the Runner is tagged.\n[subroutine] End the run if the Runner is tagged.\n[subroutine] End the run if the Runner is tagged.","subtypes":["Barrier"]}},{"qty":2,"title":"Navi Mumbai City Grid","details":{"faction":"NBN","cycle_code":"mumbad","factioncost":2,"setname":"Fear the Masses","number":110,"type":"Upgrade","title":"Navi Mumbai City Grid","uniqueness":false,"code":"10110","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/10110.png","cost":2,"deck-limit":3,"trash":3,"set_code":"ftm","subtype":"Region","text":"During a run on this server, the Runner cannot use paid abilities on non-<strong>icebreaker</strong> cards.\nLimit 1 <strong>region</strong> per server.","subtypes":["Region"]}},{"qty":3,"title":"Hydra","details":{"faction":"NBN","cycle_code":"reign-and-reverie","factioncost":4,"setname":"Reign and Reverie","number":46,"type":"ICE","title":"Hydra","uniqueness":false,"strength":6,"code":"22046","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/22046.png","cost":10,"deck-limit":3,"set_code":"rar","subtype":"Sentry - AP","text":"[subroutine] Do 3 net damage if the Runner is tagged; otherwise, give the Runner 1 tag.\n[subroutine] Gain 5[credit] if the Runner is tagged; otherwise, give the Runner 1 tag.\n[subroutine] End the run if the Runner is tagged; otherwise, give the Runner 1 tag.\n","subtypes":["Sentry","AP"]}},{"qty":3,"title":"Hedge Fund","details":{"faction":"Neutral","cycle_code":"system-gateway","factioncost":0,"setname":"System Gateway","number":75,"type":"Operation","title":"Hedge Fund","uniqueness":false,"code":"30075","side":"Corp","image":"http://localhost:1042/img/cards/overrides/sg/en/default/stock/30075.png","cost":5,"deck-limit":3,"set_code":"sg","subtype":"Transaction","text":"Gain 9[credit].","subtypes":["Transaction"]}},{"qty":3,"title":"Send a Message","details":{"faction":"Neutral","cycle_code":"system-gateway","factioncost":0,"setname":"System Gateway","agendapoints":3,"number":69,"advancementcost":5,"type":"Agenda","title":"Send a Message","uniqueness":false,"code":"30069","side":"Corp","image":"http://localhost:1042/img/cards/overrides/sg/en/default/stock/30069.png","deck-limit":3,"set_code":"sg","subtype":"Security","text":"When this agenda is scored or stolen, you may rez 1 installed piece of ice, ignoring all costs.","subtypes":["Security"]}},{"qty":3,"title":"Endless EULA","details":{"faction":"NBN","cycle_code":"kitara","factioncost":2,"setname":"The Devil and the Dragon","number":74,"type":"ICE","title":"Endless EULA","uniqueness":false,"strength":0,"code":"21074","side":"Corp","image":"http://localhost:1042/img/cards/en/default/stock/21074.png","cost":6,"deck-limit":3,"set_code":"tdatd","subtype":"Barrier","text":"[subroutine]End the run unless the Runner pays 1[credit].\n[subroutine]End the run unless the Runner pays 1[credit].\n[subroutine]End the run unless the Runner pays 1[credit].\n[subroutine]End the run unless the Runner pays 1[credit].\n[subroutine]End the run unless the Runner pays 1[credit].\n[subroutine]End the run unless the Runner pays 1[credit].","subtypes":["Barrier"]}}]}⏎

% curl -i https://jinteki.net/game/log -H "X-JNET-API: 7da39fa1-e187-4fbe-ae2a-170914228f3c"       
HTTP/1.1 200 OK
Cache-Control: no-store
Content-Type: application/json; charset=utf-8
Content-Length: 164
Server: http-kit
Date: Thu, 19 Aug 2021 17:31:12 GMT

{"messages":[{"user":"__system__","text":"test1 has created the game."},{"user":"__system__","text":"[hr]"},{"user":"__system__","text":"test1 takes a mulligan."}]}⏎

% curl -i https://jinteki.net/game/deck -H "X-JNET-API: 7da39fa1-e187-4fbe-ae2a-170914228f3c"                                      I
HTTP/1.1 200 OK
Cache-Control: no-store
Content-Type: application/json; charset=utf-8
Content-Length: 363
Server: http-kit
Date: Thu, 19 Aug 2021 17:32:08 GMT

{"cards":["10110","10110","11075","11075","11094","11094","11094","21039","21039","21039","21074","21074","21074","22039","22039","22039","22046","22046","22046","26048","26048","26114","26114","26114","26115","26115","26115","30041","30050","30050","30052","30053","30053","30053","30056","30056","30064","30064","30069","30069","30075","30075","30075","31082"]}⏎

% curl -i https://jinteki.net/game/hand -H "X-JNET-API: 7da39fa1-e187-4fbe-ae2a-170914228f3c" 
HTTP/1.1 200 OK
Cache-Control: no-store
Content-Type: application/json; charset=utf-8
Content-Length: 51
Server: http-kit
Date: Thu, 19 Aug 2021 17:32:31 GMT

{"cards":["11075","26048","30041","30056","30069"]}⏎

% curl -i https://jinteki.net/game/discard -H "X-JNET-API: 7da39fa1-e187-4fbe-ae2a-170914228f3c"
HTTP/1.1 200 OK
Cache-Control: no-store
Content-Type: application/json; charset=utf-8
Content-Length: 12
Server: http-kit
Date: Thu, 19 Aug 2021 17:32:54 GMT

{"cards":[]}⏎

# Preflight Request
% curl -H "Origin: http://bob.com" -H "Access-Control-Request-Method: GET" -H "Access-Control-Request-Headers: X-JNet-API" -X OPTIONS --verbose https://jinteki.net/game/deck
*   Trying ::1...
* Connected to localhost (::1) port 1042 (#0)
> OPTIONS /game/deck HTTP/1.1
> Host: localhost:1042
> User-Agent: curl/7.49.0
> Accept: */*
> Origin: http://bob.com
> Access-Control-Request-Method: GET
> Access-Control-Request-Headers: X-JNet-API
>
< HTTP/1.1 200 OK
< Access-Control-Allow-Methods: GET
< Access-Control-Allow-Origin: http://bob.com
< Access-Control-Allow-Headers: Accept, Accept-Encoding, Accept-Language, Authorization, Content-Type, Origin, X-Jnet-Api
< Content-Length: 18
< Server: http-kit
< Date: Thu, 19 Aug 2021 17:34:56 GMT
<
* Connection #0 to host localhost left intact
preflight complete⏎
```


