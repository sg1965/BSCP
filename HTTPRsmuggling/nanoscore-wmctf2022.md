# nanoScore - wmctf2022 write up


### Challenge Description
"If you get the first blood, you will get some rewards from Nano. Can you?" <br>
![challenge dsc](https://user-images.githubusercontent.com/45040001/186703630-fed38c87-d18b-4c61-8a9e-b92abae72653.png)

### Challenge Hint
"First Blood is the sponsor, but it is very helpful to solve the problem Therefore, the reward of this challenge belongs to second blood"
![challenge hint](https://user-images.githubusercontent.com/45040001/186704114-7f942670-8560-4991-a986-1721358bdd3f.png)


### Login and register pages
When we connect to the challenge IP we can see a login page at `login.html`. So after that i tried common login bypass techniques but nothing works like i want so i start more enumeration.
![login page](https://user-images.githubusercontent.com/45040001/186704685-d9d6cb74-e68d-48f3-8f79-87a7e0012d03.png)

I took a look in html source code and i found a register page at `register.html` which somebody can create an account and login to the web app. <br>
![challenge css](https://user-images.githubusercontent.com/45040001/186705677-7563dce6-eb72-46e1-9995-f18fc606beb6.png) <Br>
![css_with_login_register](https://user-images.githubusercontent.com/45040001/186705693-cc826f2a-e000-4dfe-9b39-931157ab1483.png) <br>
![register page](https://user-images.githubusercontent.com/45040001/186706183-b8f43a58-9790-45ad-9297-7e45b4c0b989.png)

### Register and login
We can use the registration form to create a user and login into web app and see what we can do with that
![login with creds](https://user-images.githubusercontent.com/45040001/186713903-61777eca-8555-4b53-8592-aea2c25ac56b.png)

When we successfully login with ur credentials the app will redirect us to `/flag` page but we need administrator permissions to see the content of the page.
 
Ur big problem it was how we will gain the administrator privilages. The first thing which i tried it was if it was possible somehow to decode the cookie and change the values to get the privilages but i failed because the token was secure with a random secret.
![need priv](https://user-images.githubusercontent.com/45040001/186714081-9e58a812-e615-4619-9e40-3abdf64d0a4f.png)

### Directory search
So the next step for me it was the directory enumeration. For directory enumeration i use burp suite pro and i found the `/users` directory

![admin_user](https://user-images.githubusercontent.com/45040001/186720146-dffbb7d6-8142-403b-8f9f-d388f2c83ca1.png) <br>
At the `/users` directory we can see register users and which user use first registation page and maybe he is the admin and get the flag.
