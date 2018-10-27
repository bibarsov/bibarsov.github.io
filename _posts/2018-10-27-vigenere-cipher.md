---
published: true
layout: post
date: 2018-10-27T00:00:00.000Z
categories: java
---
## Vigenere cipher

### What is it?

Vigenere cipher is a method of encryption by using polyalphabetic cipher (based on substitution, using multiple substitution alphabets). Let's have a closer look:

We have a text to encrypt, for example - JAVA. And now we need think of the key, so we couldn't guess the original word. Vigenere cipher suggests to create some keyword so we shift every letter by the number associated with a sequence number of the corresponding keyword's letter.

```
JAVA (text)
++++
BCDE (keyword)
↓↓↓↓
KCYE (result)
```

As we may see its logic is quiet simple:
J + B => 10 + 1 (for A it'll be 0) = 11, so it's K.

Moreover, there is a Vigenere table, that can help us to encrypt and decrypt our cipher quickly:

![Vigenere table]({{site.baseurl}}/assets/img/vigenere_table.png)

### Implementation
Finally, I wrote a program to implement encryption logic of Vigenere cipher:

```java
    public static void main(String[] args) {
        final String textToEncrypt = args[0];
        final String key = args[1];
        final char A = 'A';
        final char Z = 'Z';

        StringBuilder encrypted = new StringBuilder();

        for (int i = 0; i < textToEncrypt.length(); i++) {
            char k = i >= key.length() ? key.charAt(i % key.length()) : key.charAt(i);
            char enc = (char) (k + textToEncrypt.charAt(i) - A);

            if (enc > Z) {
                enc = (char) (enc % Z + A - 1);
            }
            encrypted.append(enc);
        }
        System.out.println(encrypted.toString());
    }
```
Try it out! :)