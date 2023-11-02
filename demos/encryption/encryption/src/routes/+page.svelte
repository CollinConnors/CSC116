<script>
	let user_plaintext = '';
    let password = '';
    let key = '';
    let ciphertext = '';
    let user_ciphertext = '';
    let plaintext = '';
    let iv =crypto.getRandomValues(new Uint8Array(16));
    console.log(iv);

     async function calculateHash() {
        const encoder = new TextEncoder();
        const data = encoder.encode(password);
        const buffer = await crypto.subtle.digest('SHA-256', data);
        const hashArray = Array.from(new Uint8Array(buffer));
        const hashHex = hashArray.map(byte => byte.toString(16).padStart(2, '0')).join('');
        key = hashHex;
        encryptText();
        decryptText();
    }

    async function encryptText() {
    const encoder = new TextEncoder();
    const data = encoder.encode(user_plaintext);
    
    // Hash the password to create a key
    const passwordBuffer = encoder.encode(password);
    const keyBuffer = await crypto.subtle.digest('SHA-256', passwordBuffer);

    // Derive a CryptoKey from the hashed password
    const cryptoKey = await crypto.subtle.importKey(
      'raw',
      keyBuffer,
      { name: 'AES-CBC' , length: 256},
      false,
      ['encrypt']
    );
    // Use the hashed password as the encryption key
    const encryptedData = await crypto.subtle.encrypt(
      {
        name: 'AES-CBC',
        iv: iv, // Initialization vector (IV)
      },
      cryptoKey,
      data
    );

    // Convert the encrypted data to a hex string and uppercase it
    //const decode = new TextDecoder().decode(encryptedData);
    const encryptedArray = Array.from(new Uint8Array(encryptedData));
    const encryptedHex = encryptedArray.map(byte => byte.toString(16).padStart(2, '0')).join('');
    ciphertext = encryptedHex;
  }

  async function decryptText() {
    const encoder = new TextEncoder();
    const decoder = new TextDecoder();

    // Hash the password to create a key
    const passwordBuffer = encoder.encode(password);
    const keyBuffer = await crypto.subtle.digest('SHA-256', passwordBuffer);

    // Derive a CryptoKey from the hashed password
    const key = await crypto.subtle.importKey(
      'raw',
      keyBuffer,
      { name: 'AES-CBC', length: 256 }, 
      false,
      ['decrypt']
    );

    // Convert the hexadecimal ciphertext to a Uint8Array
    const hexArray = user_ciphertext.match(/.{1,2}/g).map(byte => parseInt(byte, 16));
    const encryptedData = new Uint8Array(hexArray);

    // Decrypt the ciphertext back to original text
    try{
        const decryptedBuffer = await crypto.subtle.decrypt(
        // @ts-ignore
        { name: 'AES-CBC', iv },
        key,
        encryptedData
        );

        // Convert the decrypted data to text
        plaintext = decoder.decode(decryptedBuffer);
    }
    catch(err){
        plaintext = "Invalid Ciphertext";
    }
  }
 

</script>

<h1>AES-CBC</h1>
<h2>Encryption</h2>
<label for="plaintext">Plaintext: </label>
<input bind:value={user_plaintext} on:input={encryptText} id="plaintext" placeholder="Enter Plaintext" />


<label for="key">Password: </label>
<input bind:value={password} on:input={calculateHash} id="key" placeholder="Enter key" />

<p>Key: {key}</p>

<p>Ciphertext: {ciphertext}</p>

<h1>Decryption</h1>
<label for="ciphertext">Ciphertext: </label>
<input bind:value={user_ciphertext} on:input={decryptText} id="ciphertext" placeholder="Enter Ciphertext" />


<label for="key">Password: </label>
<input bind:value={password} on:input={calculateHash} id="key" placeholder="Enter key" />

<p>Key: {key}</p>

<p>Plaintext: {plaintext}</p>