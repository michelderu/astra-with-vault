# 🔐 DataStax Astra and Vault (or any other secret/value provider)
Using Vault (or a similar product) is a great idea to keep your environment secured. But how do you manage the Secure Connect Bundle that connects you to DataStax Astra? Surely, uploading it to your github repo is not an option. With the help of our friends at Fiverr who ran into this issue, here's a simple solution to it. The code samples are based on Kotlin, but it should be resonably self-explanatory to transform to another language.

## Step 1️⃣ - Encode the Secure Connect Bundle to Base64
Locally use your application code to encode (ISO compliant) the zip to a Base64. You do this once:
```java
val f = File("/path-to/SecureConnectBundle.zip").readText(Charsets.ISO_8859_1)
val b64 = Base64.getEncoder().encodeToString(f.toByteArray(Charsets.ISO_8859_1))
```
Alternatively you can also use the `base64` cli available on most Linux distributions:
```sh
base64 -w 0 /path-to/SecureConnectBundle.zip
```

## Step 2️⃣ - Store the resulting string in your provider
Copy the string into any secret / value provider service of your choosing.

## Step 3️⃣ - Use the string in your driver connection setup
In the driver config, upon retrieval of the secret, decode it and supply it as a byte stream to the driver. The variable `base64SecureConnectZip` contains the Base64 encoded string from Vault.
```java
fun secureConnectBundleInputStream(): ByteArrayInputStream {
    val decodedSecureConnectZip = Base64.getDecoder().decode(base64SecureConnectZip)
    val inputStream = ByteArrayInputStream(decodedSecureConnectZip)

    return inputStream
}

sessionBuilder.withCloudSecureConnectBundle(secureConnectBundleInputStream())
```
