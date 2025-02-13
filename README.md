# Post-Quantum P2P

### OpenSSL

```bash
# create private key and self-signed root certificate for the certificate authority
openssl req -x509 -new -newkey dilithium5 -keyout ca-cert-pkey.pem -out ca-cert.pem -config /workspaces/pqp2p/openssl/ca.cnf
# create private key and certificate signing request for the peer
openssl req -new -newkey dilithium5 -keyout cert-pkey.pem -out csr.pem -config /workspaces/pqp2p/openssl/peer.cnf
# issue certificate from the certificate authority
openssl ca -in csr.pem -out cert.pem -cert ca-cert.pem -keyfile ca-cert-pkey.pem -config /workspaces/pqp2p/openssl/ca.cnf
# generate public-private key pair
openssl genpkey -algorithm dilithium5 -out sig-pkey.pem -outpubkey sig-pubkey.pem -aes256 -provider oqsprovider -provider default
# verify file signature
openssl pkeyutl -verify -pubin -inkey sig-pubkey.pem -in data.txt -sigfile data.txt.sig -provider oqsprovider -provider default
```

### [uguu.se](https://uguu.se/)

```bash
# upload file
curl -F files[]=@data.txt https://uguu.se/upload
# download file
curl -o data.txt https://d.uguu.se/OlATWQRq
```

### Komendy dla peera (alice - nadawca)

```bash
# create private key and certificate signing request for the peer
openssl req -new -newkey dilithium5 -keyout cert-pkey.pem -out csr.pem
#przeslanie csr do CA
curl -F "file=@data.txt" https://file.io
#odebranie pliku od CA
curl -o data.txt https://file.io
# generate public-private key pair
openssl genpkey -algorithm dilithium5 -out sig-pkey.pem -outpubkey sig-pubkey.pem -aes256
#przeslanie klucza publicznego do boba
curl -F "file=@data.txt" https://file.io
#odebranie klucza publicznego od boba
curl -o data.txt https://file.io
#przeslij plik
0.0.1 cert.pem cert-pkey.pem ca-cert.pem sig-pkey.pem
```

### Dodatkowo dla odbiorcy:

```bash
#odbierz plik
pqp2p 127.0.0.1 cert.pem cert-pkey.pem ca-cert.pem sig-pkey.pem
#zweryfikuj poprawnosc pliku
openssl pkeyutl -verify -pubin -inkey klucz-peera.pem -in plik.txt -sigfile plik.txt.sig
```
