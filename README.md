# Decentralized Poker Card Games

Trustless, dealerless, p2p poker card games to prevent the house or peers from cheating.

## How it works
Commutative encryption is the underlying methodology for this application. It is by far the most secure method, and 1024 key size for RSA should be secure enough for the duration of the game, while taking roughly constant time with PyCrypto. The game will go as follows:

1. Everyone will encrypt the deck with their public key and shuffle once
2. Each player then take turns to decrypt with the corresponding private key then re-encrypt with a different public key for each card. 
3. Draw hand by asking for everyone's private key for that card

Some shortcuts can be taken (draw hand between steps 1 and 2) but this is generally how it will go.

## Cheating / Collusion
- RSA key size has to be secure against the other 8 players colluding against the unknowing victim for the duration of the game.
- It is possible for dishonest peers to attempt to rig the deck. Chosen plaintext attacks possibly allow dishonest peers to tamper with cards to a certain extent. Peers are also able to corrupt the data of certain cards rendering it unplayable for peers and/or cause inconsistent decks (e.g. player 1 could tamper with his draws to produce an Ace, causing the deck to have one extra Ace). Hence there must be some sort of structure to identify these dishonest peers and penalize them.

## Prevention
Effects of cheating can be mitigated through having a ledger recording every action of the players and incentivizing honest peers to expose their malicious counterparts. Specifically in this case, players will record the result of each step with some evidence, such as the hash of certain secrets. Generally, this forces players to participate in a commitment scheme with secrets they intend to use, then allowing peers to later check if H(secret) matches that announced earlier, and more importantly the intended result is accurate e.g. f(Dn-1, secret) = the observed Dn.

For instance, in step 1 each player will announce to other peers via the ledger (e.g. blockchain) the result of their encryption, along with the public key and the hash of the shuffle order. When required, players can submit the proof (plaintext of shuffle order) allowing others to verify that the deck Dn = f(Dn-1, order, pubkey) as well as check whether the hashes match. We can do the same check for step 2.

Step 3 will be automatically verifiable as players have to reveal all the private keys to play a card.

Doing this allows all the peers to verify that the peer followed the exact same steps the game is supposed to be played out, ensuring integrity in the deck and results.

Verification will no doubt be relatively resource intensive, and should optimally only be done on request by the peers. Peers will be rewarded for exposing dishonest acts, and dishonest peers should lose everything they have already put in the current game. Since it most definitely causes players to reveal their secrets, verification should be done as a post-game step before handing out rewards, to prevent players from prematurely ending the game by calling for a verification (perhaps to prevent losing too much money when colluding with others).

## Known Issues
- Without a central authority, there is no guarantee that the deck is not tampered with. Colluding players can team up and have a scapegoat ruin the deck when the game is not going their favor to potentially minimize losses. If this is a prevalent problem having a central authority might be better, but players will have to trust that the authority is not colluding with other players which kind of defeats the purpose.
- Block time might slow down the game to unacceptable levels. However the issue is solved if steps 1 and 2 are done off chain via RTC, and the game can go in parallel with the commitment scheme, which is most probably required only post-game.
- Games will have to be restarted if a player leaves as the keys will be lost. A (not very good) possibility is to have players commit the secrets into a smart contract via another address, which will release the secrets upon detecting the event of said player leaving.
- It seems I was mistaken into thinking RSA is commutative. Another commutative encryption scheme secure for the game duration will have to be found.