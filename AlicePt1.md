# pwnteras_love_ctf
## Alice pt1 
## Description
Author: Idk

Hola
Me dieron tu contacto porque me dijero que eres Ingeniero en Ciberseguridad en la UP... sabes hackear?
Es que mira, tengo planeado salir este 14 de febrero con mi novio pero nose, lo he notado raro ultimamente. Talvez sea yo pero no lo se.
Ademas, mi amiga me dijo que lo encontro en esta app de citas llamada <span style="color:#1e90ff;">dating.pwnteras.dev</span>, pero la verdad no le creo del todo. Ni se si quiera creerle...
Ayudame a buscarlo si es que esta en esa app, se llama Diego Williams.


## Files

-No file is provided

## Analysis

Upon entering the page for the challenge, we see that it's a copy of Tinder. 
We use the page's search bar to find Diego Williams' profile.

By liking Diego we match and can start chatting.

After chatting with Diego, he will give us this image:

<img width="1602" height="935" alt="justice_league" src="https://github.com/user-attachments/assets/0c73a4c8-e99f-40a8-8df3-7cb173651f3d" />

## Theory
- Google Reverse Search: Google is a powerful tool for analyzing visual patterns and finding matches across the internet.

- what3word: A system that divides the world into 3x3 squares, each assigned to a unique combination of words.

## Solution
1. When you put the image into Google's image search, the name of the cinema and several web pages with its exact location will automatically appear.
   
<img width="1448" height="813" alt="Captura de pantalla 2026-02-12 175305" src="https://github.com/user-attachments/assets/5fc1e76a-2064-4bc0-9272-b09a228553c1" />

This is the location:

<img width="1022" height="129" alt="Captura de pantalla 2026-02-12 175451" src="https://github.com/user-attachments/assets/548fe29f-f78a-4a5c-b64b-f8de2e465459" />

Entering that location into the website what3word will take you to the location of the cinema; you just need to find the correct street.

<img width="1909" height="834" alt="Captura de pantalla 2026-02-12 175724" src="https://github.com/user-attachments/assets/903d732f-d7eb-4273-97f7-81b696fdeb7d" />

After several failed attempts, and changing the language of the website, I decided to review the challenge and realized that at the end Diego asks you to send the 3 words to his Instagram, so I decided to look for his username on Instagram.

<img width="1340" height="792" alt="Captura de pantalla 2026-02-12 175920" src="https://github.com/user-attachments/assets/25d75bae-235c-4f6c-936e-ae912dc25120" />

I decided to send the 3 words to Diego via Instagram.

<img width="1329" height="816" alt="Captura de pantalla 2026-02-12 175840" src="https://github.com/user-attachments/assets/3e83831f-8ce1-489d-99d7-e45e99c01cec" />

It was the same flag I had already posted, but the page had some kind of error 
## Flag
>pwnteras_love{grumbles.remind.detection}

## How to avoid
- Avoid taking photos near famous buildings, or simply don't send photos to strangers on Tinder.


## Alice pt2 
## Description
Author: Idk

Hola de nuevo. Mientras buscas que es de mi novio, te pido que cheques su IG. Su ultimo post tiene un mensaje algo... raro.


## Files

-No file is provided, but we have the boyfriend's IG

## Analysis

Since Diego blocked me, I had to log into my secondary Instagram account to find out what his last post said.
Upon entering your last post we see that in the description of the photo there is a somewhat strange message.

<img width="1342" height="790" alt="Captura de pantalla 2026-02-12 180052" src="https://github.com/user-attachments/assets/61aebb29-206b-4aca-8062-1394c1d2b01e" />

To find out if it is encryption, we will use two websites, one called Cyberchef (https://gchq.github.io/CyberChef/) to decrypt the messages and another called dcode (https://www.dcode.fr/) where we will use the cipher-identifier tool to identify the type of encryption.
By putting the text inside dcode, it gives us that there is a high probability that it is base64.

<img width="467" height="337" alt="Captura de pantalla 2026-02-13 104129" src="https://github.com/user-attachments/assets/52fe5739-5f18-438e-9e04-3f249f4d0196" />

## Theory
- Codification: Converting information using a series of pre-established rules, so that the data can be stored, transmitted or interpreted correctly, basically it is translating
- Base64: Convert binary to readable text

## Solution
1. When we convert the text to base64 we see a clear flag structure, but there is still no readable text which suggests that more encryption is needed.
> cjagrenf_ybir{l0h_pe4xrq_Gur_RA1TZN_P0Q3_019320}
2. By measuring the text again in the cipher-identifier we see the following.

<img width="422" height="295" alt="Captura de pantalla 2026-02-13 104313" src="https://github.com/user-attachments/assets/d4cb06a4-15c2-4514-9bf4-721147d46761" />

3. When all the operations are applied, the flag finally appears.

<img width="1911" height="783" alt="Captura de pantalla 2026-02-13 104355" src="https://github.com/user-attachments/assets/11585e2b-754b-4dc8-8409-db327062ba9e" />


## Flag
>pwnteras_love{y0u_cr4ked_The_EN1GMA_C0D3_019320}

## How to avoid
- Avoid uploading any confidential information to a public place like Instagram, especially with encodings as common as Base64. If you want to hide things, you can use secret keys.
