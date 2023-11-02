A demo of symetric key (private key) encrytion using [AES-CBC](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation)

# To Run
1. In your terminal `docker compose -f "demos\encryption\dockercompose.yml" up -d --build`
2. In your browser navigate to [http://localhost/](http://localhost/)
3. Enter a Plaintext phrase and a Key to generate a ciphertext
4. Enter the ciphertext in the decryption section to decrypt the message