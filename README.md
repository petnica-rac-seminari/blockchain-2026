# Pixel Chain

Napisaćete čvor: program koji, zajedno sa čvorovima vaših kolega iz tima, održava jedno zajedničko platno od piksela. Niko ne pokreće server i niko nije glavni. Svaki čvor piše druga osoba, možda u drugom jeziku, a ipak svi moraju da prikazuju potpuno istu sliku.

Do kraja, vaša grupa od 4–5 polaznika pokreće sve svoje čvorove zajedno. Rudarite zemljište, bojite ga, trgujete njime — a jedan čvor se nasilno ugasi usred rada i mora da se vrati.

## Ideja ukratko

**Identitet.** Krajnjeg korisnika predstavlja par ključeva. Vaš javni ključ je vaše ime; vaš privatni ključ dokazuje da ste to vi. Nema naloga ni lozinki: ko god može da potpiše ključem, poseduje ono što tom ključu pripada. Izgubite ključ, izgubili ste zemljište.

**Transakcije.** Zemljištu mogu da se dese dve stvari:

- **bojenje** — vlasnik postavlja njegovu boju;
- **prenos** — vlasnik ga daje drugom javnom ključu.

Obe potpisuje trenutni vlasnik zemljišta. Zemljište se razmenjuje za usluge u stvarnom životu — lanac samo beleži ko je vlasnik čega.

**Blokovi.** Transakcije se skupljaju u blokove. Svaki blok sadrži heš bloka pre njega, pa blokovi čine lanac, a izmena bilo čega u starom bloku pokvari svaki heš posle njega.

**Rudarenje.** Da biste dodali blok, morate da nađete broj (nonce bloka) zbog kog heš bloka počinje određenim brojem nultih bitova. Jedini način da ga nađete je da pokušavate, iznova i iznova. To je *proof of work* (PoW).

**Zemljište.** Rudarenje bloka je jedini način da se pojavi novo zemljište. Heš bloka odlučuje — nasumično — koju parcelu rudar dobija. Niko ne može da bira. Brži računari dobijaju više zemljišta; i to je deo lekcije.

**Dogovor.** Svaki čvor kreće od istog prvog bloka (*genesis*), primenjuje iste transakcije istim redosledom, po istim pravilima, i tako završi sa istim platnom. Ponekad dva čvora izrudare blok u isto vreme i lanac se račva — to je *fork*. To je normalno. Pravilo koje to razrešava: **pobeđuje lanac sa najviše akumuliranog rada.**

## Šta je fiksno, a šta odlučuje vaša grupa

Radionica određuje igru: proof of work, zemljište nasumično izabrano iz heša bloka, prenos i bojenje koje potpisuje vlasnik. Sve ostalo je na vašoj grupi, i različite grupe će završiti sa različitim, međusobno nekompatibilnim lancima. To je namerno.

| Odluka                                              | Ko odlučuje | Napomene                                  |
|-----------------------------------------------------|-------------|-------------------------------------------|
| Programski jezik                                    | Vi          | Šta god želite                            |
| Šema potpisa                                        | Grupa       | Preporučujemo Ed25519                     |
| Heš funkcija                                        | Grupa       | Preporučujemo SHA-256                     |
| Format poruka i način na koji čvorovi komuniciraju  | Grupa       |                                           |
| Detalji pravila za forkove                          | Grupa       | Pobeđuje najviše akumuliranog rada — ostalo je na vama |
| Vreme bloka, težina, veličina platna                | Grupa       | Pogledajte predložene opsege ispod        |
| Broj parcela po bloku; šta ako je parcela zauzeta   | Grupa       |                                           |
| Koji bajtovi heša biraju parcelu; genesis blok      | Grupa       |                                           |

**Predloženi opsezi.** Platno od 16×16 do 64×64. Po jedan blok negde u grupi na svakih 5–30 sekundi. Težina se meri u bitovima: za heš sa *d* vodećih nultih bitova treba oko 2^*d* pokušaja. Izmerite koliko heševa u sekundi vaš čvor stigne, i imajte na umu da je svačiji računar drugačiji.

## Grupna specifikacija

Pre nego što napiše mrežni kod, vaša grupa piše specifikaciju: kratak dokument koji svaki čvor prati. Dva pravila:

1. **Bajtovi moraju tačno da se poklapaju.** Ako vaš čvor i čvor vašeg kolege izračunaju heš istog bloka, moraju da dobiju isti rezultat — ne „skoro“ isti, nego *isti*. Zapišite tačno koji bajtovi se hešuju i potpisuju.
2. **Specifikacija ima prednost.** Kada se dva čvora ne slažu, ničiji kod nije unapred u pravu. Proverite prema specifikaciji. Ako specifikacija nema odgovor, prvo ispravite specifikaciju, dogovorite se kao grupa, pa tek onda ispravite kod.

Vaša specifikacija mora da odgovori bar na ova pitanja:

- Koja šema potpisa i koja heš funkcija? Kako se zapisuju ključevi, heševi i potpisi (hex? base64? velika ili mala slova)?
- Kako izgleda transakcija? Koji tačno bajtovi se potpisuju?
- Kako čvor zna da transakcija nije ponovljena — ista potpisana poruka poslata dvaput?
- Kako izgleda blok? Koji tačno bajtovi se hešuju?
- Kako izgleda genesis blok?
- Koliko rada blok mora da pokaže? Koji bajtovi heša biraju parcelu i koliko parcela blok dodeljuje? Šta ako parcela već ima vlasnika?
- Kada su blok i transakcija ispravni?
- Koji lanac pobeđuje? Šta ako su dva lanca podjednako dobra?
- Kako čvorovi jedni drugima šalju transakcije i blokove? Kako čvor traži blok koji mu nedostaje?
- Šta čvor radi kada se kasnije priključi ili se ponovo pokrene?
- Kako se simulira lag: kolika su kašnjenja i koliko često se poruke gube?

Uključite **test podatke**: nekoliko primera ključeva, transakcija i blokova, sa očekivanim heševima i potpisima. Svako proverava svoj kod prema njima pre nego što razgovara sa bilo čijim drugim čvorom.

Specifikaciju napišite sami, kao grupa, bez veštačke inteligencije. Za kod koristite veštačku inteligenciju koliko god želite.

## Zahtevi

Svaka grupa treba da ispuni sve ovo:

1. Generisati ključeve; potpisivati i proveravati transakcije.
2. Rudariti blokove pomoću PoW-a; dodeljivati zemljište na osnovu heša bloka.
3. Proveravati ispravnost blokova i transakcija, uključujući zaštitu od ponovljenih transakcija.
4. Slati transakcije i blokove između čvorova. Svaki čvor se direktno povezuje sa svakim drugim čvorom.
5. Razrešavati forkove i omogućiti čvoru da sustigne ostale kada se kasnije priključi.
6. Podneti iznenadni pad ili odlazak čvorova.
7. Podržati prenos i bojenje zemljišta.
8. Prikazati platno nekako — čak je i generisana slika u redu.
9. Simulirati lag mreže. Svi ćete biti na jednoj mreži ili čak na jednom računaru, gde poruke stižu odmah i nikad se ne gube — za razliku od pravog interneta. Zato svaka poruka koju vaš čvor pošalje dobija nasumično kašnjenje i ponekad se izgubi. Opcija to uključuje i isključuje. **Držite je uključenom.**

## Milestone-ovi

| #  | Milestone          | Ko           | Gotovo kada                                                                                     |
|----|--------------------|--------------|-------------------------------------------------------------------------------------------------|
| 0  | Alati              | Svako od vas | Umete da generišete ključ, potpišete i proverite poruku i izračunate SHA-256 heš u svom jeziku |
| 1  | Grupna specifikacija | Grupa      | Specifikacija odgovara na svako pitanje iznad i sadrži test podatke                            |
| 2  | Samostalni čvor    | Svako od vas | Vaš čvor prolazi test podatke, sam rudari lanac, gradi platno i prikazuje ga                   |
| 3  | Prvi kontakt       | Parovi       | Dva čvora različitih ljudi razmenjuju transakcije i blokove, sa uključenim lagom, i slažu se oko platna |
| 4  | Cela mreža         | Grupa        | Svi čvorovi rade zajedno sa lagom. Forkovi se dešavaju i razrešavaju; čvor koji se kasnije priključi sustiže ostale |
| 5  | Padovi             | Grupa        | Gašenje bilo kog čvora ne zaustavlja ostale, a ugašeni čvor se ponovo priključuje              |
| 6  | Završni test       | Grupa        | Prolazite završni test dok mentor posmatra                                                      |

Ako ranije stignete do milestone-a 2, nađite kolegu iz tima koji je takođe tu i započnite milestone 3 — a zatim pomozite ostalima.

## Završni test

1. Pokrenite sve čvorove sa uključenim lagom. Pustite ih da neko vreme rudare, boje i prenose zemljište.
2. Nasilno ugasite jedan čvor (ne uredno gašenje — ubijte proces) dok ostali nastavljaju da rade.
3. Proverite da preostali čvorovi nastavljaju rad bez zaglavljivanja.
4. Ponovo pokrenite ugašeni čvor i pustite ga da sustigne ostale.
5. Zaustavite svu aktivnost, uključujući rudarenje. Svaki čvor mora da stigne do istog poslednjeg bloka i da prikazuje isto platno.

## Dodatni ciljevi

Za kada prođete završni test. Cilj se računa samo kada ga ima **svaki** čvor u grupi, zato pomažite jedni drugima. Mogu da se rade u bilo kom redosledu.

- Povezivanje sa drugom grupom (detalje dogovarate sa mentorom)
- Atomska razmena: dva vlasnika razmenjuju parcele u jednoj transakciji, ili ne razmenjuje niko
- Prilagođavanje težine
- Delegirano bojenje: vlasnik dozvoljava drugom ključu da boji njegovo zemljište
- Retka mreža: čvorovi se povezuju samo sa nekim čvorovima i prosleđuju poruke dalje
- Preživeti mentorov napad lažnim ili neispravnim porukama
- Preživeti podelu mreže: dve polovine rudare odvojeno, ponovo se povežu i opet se usaglase
- Čuvati lanac na disku i oporaviti se posle ponovnog pokretanja
