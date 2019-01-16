# NGSI-Encryption-Layer-as-a-Service

This document describes the encryption service developed at ITESM as a tool for encrypting and decrypting [FIWARE data models](https://www.fiware.org/developers/data-models/). 

[FIWARE](https://www.fiware.org/) is a curated framework of open source platform components to accelerate the development of Smart solutions. The [FIWARE platform](https://www.fiware.org/developers/catalogue/) provides a rather simple yet powerful set of APIs (Application Programming Interfaces) that ease the development of Smart Applications in multiple vertical sectors. 

The main and only mandatory component of any "Powered by FIWARE" platform or solution is the [FIWARE Orion Context Broker Generic Enabler](https://fiware-orion.readthedocs.io/en/master/), which brings a cornerstone function in any smart solution: the need to manage context information in a highly decentralized and large-scale manner, enabling to perform updates and bring access to context.

[FIWARE data models](https://www.fiware.org/developers/data-models/) have been harmonized to enable data portability for different applications including, but not limited, to Smart Cities. They are intended to be used together with [FIWARE NGSI version 2](https://www.fiware.org/2016/06/08/fiware-ngsi-version-2-release-candidate/).

The application can be seen as two stand-alone services, one that uses tokens as a security measure and the second one that uses sessions as a security measure. Both stand-alone services enable the encryption and decryption of all up-to-date available FIWARE data models published in [FIWARE Data Models official site](https://www.fiware.org/developers/data-models/). 
First, the overall overview of the encryption service is described; secondly, the stand-alone service that uses tokens is introduced; lastly, the stand-alone service that uses sessions is detailed.


## Prerequisites
The encryption service can be installed on any Operative System.

The following software must be previously installed in the server which will hold the encryption service.
1. [Docker](https://www.docker.com/get-started)
1. [Postman](https://www.getpostman.com/apps)/[Insomnia](https://insomnia.rest/download/)

Furthermore, the following ports containers are required.
1. ngsi_nodejs 8000 (only for the token-based service)
1. ngsi_python 2121 (only for the session-based service)


## How to use
In the following, the service specifications are described. This specification applies to both stand-alone services mentioned above. 
The inputs that the encryption service expects are listed below.
1. Orion URL Source or a JSON payload
2. Orion URL Sink or a JSON payload
3. User name
4. User email
5. Keys generated by the encryption process
6. Token generated by the encryption service

In the following are described several details about the encryption service.

* Due to context broker’s limits on attribute lengths, the encryption algorithm relies on 8-bit keys.
* For each attribute of the JSON payload, a key is generated. All the keys are integrated into a single file. This single file is then sent to the user via email.

The services that both stand-alone implemented services comprises are:
* Sign up
* Sign in
* Encryption of a local JSON file
* Encryption of entities from an Orion Context Broker URL
* Encryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL
* Decryption of a local JSON file 
* Decryption of entities from an Orion Context Broker URL
* Decryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL

The initial authentication procedure carried out by the encryption service is performed via an email verification process. This process allows a user to authenticate itself within the service. It is noteworthy that the authentication procedure currently works only with Gmail accounts. The process is simplified as follows:

```
Input: User name (#3 in the overall input definition section) and User email (#4 in the overall input definition section)
Output: Token generated by the encryption service
```

Postman/Insomnia specifications
```
HTTP endpoint:
	POST
	Uri: http://ngsi-edlayer/signup
	
	Params (Form URL Encoded):
		name: User name (3)
		email: User email (4)
	
	Headers:
		Fiware-Service:'default'
		Fiware-ServicePath:'/'

```


## A walkthrough
### Token-based service
The stand-alone encryption service that uses tokens as a verification measure is described below.

#### Deployment and execution instructions
The following procedure denotes the instructions to deploy and execute the encryption service that will reside in the server.

1. Locate at the service folder location: ```cd ../NGSI_NODEJS_DOCKER```

	![imagen1](https://user-images.githubusercontent.com/39604832/46701972-2d23b980-cbe7-11e8-9010-24025322c196.png)

2. Execute the docker pull command: ```docker pull ngsi/encryption_layer_as_a_service_nodejs```
3. Start the docker compose: ```sudo docker-compose up -d```

	![imagen2](https://user-images.githubusercontent.com/39604832/46699889-c6030680-cbe0-11e8-8fe3-8c19b8ae3e7b.png)

4. Check docker containers status: ```docker ps```

	![imagen3](https://user-images.githubusercontent.com/39604832/46701974-2d23b980-cbe7-11e8-9139-03e789c82c44.png)
	![imagen4](https://user-images.githubusercontent.com/39604832/46701975-2d23b980-cbe7-11e8-8de1-fa66adfac05b.png)

5. Verify that the service is up and running: ```http://localhost:8000```

	![imagen5](https://user-images.githubusercontent.com/39604832/46701976-2d23b980-cbe7-11e8-8345-385e0cca8aea.png) 

#### Sign up
This service allows the user to authenticate itself for the first time into the encryption service.
```
Input:
	3 and 4
Output:
	6 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/signup
		Input params:
			name: 3
			email: 4
```

   ![imagen6](https://user-images.githubusercontent.com/39604832/46701977-2dbc5000-cbe7-11e8-9a5d-9cf84960f03d.png)

#### Sign in
This service allows the user to authenticate itself into the encryption service and use the several encryption and decryption services only if the user loses his token.
```
Input:
	3 and 4
Output:
	6 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/authenticate
		Input params:
			name: 3
			email: 4
```

   ![imagen7](https://user-images.githubusercontent.com/39604832/46701978-2dbc5000-cbe7-11e8-9e23-bf5d77eee244.png)   
#### Encryption of a local JSON file
This process shows an example of how a user uploads a local JSON file into the service, encrypt it, and then view the encrypted JSON file through the postman/insomnia interface and receive the keys for decrypt the JSON file via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface. This service does not send the encrypted JSON file to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1 and 6
Output:
	2 via postman/insomnia interface and 5 via email

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/encrypt
		Input params:
			json: document in JSON format
		Headers:
			Authorization: user token
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![imagen8](https://user-images.githubusercontent.com/39604832/46701979-2dbc5000-cbe7-11e8-9580-ba66eafd3bbf.png)
   ![imagen9](https://user-images.githubusercontent.com/39604832/46701980-2dbc5000-cbe7-11e8-9b41-35bb4544d976.png)
   ![imagen10](https://user-images.githubusercontent.com/39604832/46701981-2dbc5000-cbe7-11e8-861d-52afe1390885.png)

#### Encryption of entities from an Orion Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL, encrypt them, and then view the encrypted entities through the postman/insomnia interface and receive the keys for decrypt the JSON file via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface. This service does not send the encrypted entities to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1 and 2
Output:
	2 via postman/insomnia interface and 5 via email

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/encrypt/ocb-local
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
		Headers:
			Authorization: user token
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![image10](https://user-images.githubusercontent.com/38046220/48510388-64563d80-e819-11e8-8eb7-d8f77d936095.png)

#### Encryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL or uploads a local JSON file, encrypt them/it, and then send the encrypted entities to an Orion Context Broker URL. Furthermore, the service shows the encrypted JSON file or entities through the postman/insomnia interface and the user receive the keys for decrypt the information via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface.
```
Input:
	1 and 2
Output:
	2 via postman/insomnia interface and 5 via email

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/encrypt/ocb
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			urlTo: http://192.168.10.193:1026/v2/entities
		Headers:
			Authorization: user token
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![imagen13](https://user-images.githubusercontent.com/39604832/46701984-2e54e680-cbe7-11e8-80fc-b3d0452e90a8.png)

#### Decryption of a local JSON file
This process shows an example of how a user uploads a local encrypted JSON file and its corresponding key's file into the service, decrypt it, and then view the decrypted JSON file through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface. This service does not send the decrypted JSON file to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1 and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/decrypt
		Input params:
			json: document in JSON format
			key: keys sent to users email
		Headers:
			Authorization: user token
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![imagen11](https://user-images.githubusercontent.com/39604832/46701982-2dbc5000-cbe7-11e8-9880-36b1d72016a0.png)
   ![imagen12](https://user-images.githubusercontent.com/39604832/46701983-2dbc5000-cbe7-11e8-9d83-0f4085cbc028.png)

#### Decryption of entities from an Orion Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL and uploads its corresponding key's file into the service, decrypt them, and then view the decrypted entities through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface. This service does not send the decrypted entities to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1, 2, and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/decrypt/ocb-local
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			key : keys sent to users email
		Headers:
			Authorization: user token
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![image11](https://user-images.githubusercontent.com/38046220/48510488-a1bacb00-e819-11e8-9f66-3e57c73bd7b2.png)

#### Decryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL or uploads a local JSON file with its corresponding key's file, decrypt them/it, and then send the decrypted entities to an Orion Context Broker URL. Furthermore, the service shows the decrypted JSON file or entities through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface.
```
Input:
	1, 2, and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:8000/decrypt/ocb
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			urlTo: http://192.168.10.193:1026/v2/entities
			key : keys sent to users email
		Headers:
			Authorization: user token
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![imagen14](https://user-images.githubusercontent.com/39604832/46701986-2e54e680-cbe7-11e8-9a8d-257a5e7aeb47.png)


### Session-based service
The stand-alone encryption service that uses sessions as a verification measure is described below.

#### Deployment and execution instructions
The following procedure denotes the instructions to deploy and execute the encryption service that will reside in the server.

1. Locate at the service folder location: ```cd ../NGSI_PYTHON_DOCKER/```

	![imagen15](https://user-images.githubusercontent.com/39604832/46701987-2e54e680-cbe7-11e8-9634-c62be7b676d9.png)

2. Create a new folder named “data”: ```mkdir data```

	![imagen16](https://user-images.githubusercontent.com/39604832/46701988-2e54e680-cbe7-11e8-8e7f-e00182ba71f8.png)

3. Start the docker compose: ```sudo docker-compose up -d```

	![imagen17](https://user-images.githubusercontent.com/39604832/46701990-2e54e680-cbe7-11e8-8602-270fa121f8a4.png)

4. Check docker containers status: ```docker ps```

	![imagen18](https://user-images.githubusercontent.com/39604832/46701991-2e54e680-cbe7-11e8-84fc-18a46010a29d.png)

5. Verify that the service is up and running: ```http://localhost:2121```

	![imagen19](https://user-images.githubusercontent.com/39604832/46701992-2eed7d00-cbe7-11e8-8603-8dd47f8066e4.png)

#### Sign up
This service allows the user to authenticate itself for the first time into the encryption service.
```
Input:
	3 and 4
Output:
	Verification via email

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/signup
		Input params:
			name: 3
			email: 4
			password: user password
```

   ![imagen20](https://user-images.githubusercontent.com/39604832/46701993-2eed7d00-cbe7-11e8-8c5d-59ed7fd86c19.png)
   ![imagen21](https://user-images.githubusercontent.com/39604832/46701994-2eed7d00-cbe7-11e8-8d98-d622df50e96a.png)

#### Sign in
This service allows the user to authenticate itself into the encryption service and use the several encryption and decryption services.
```
Input:
	3
Output:
	6 via email or postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/login
		Input params:
			name: 3
			password: user password
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![10 formulario body](https://user-images.githubusercontent.com/38957081/51272661-54c9b100-1990-11e9-88e6-6d56a694976c.png)
   ![11 respuesta sigup](https://user-images.githubusercontent.com/38957081/51272662-54c9b100-1990-11e9-8bcc-dcf02572f846.png)

#### Encryption of a local JSON file
This process shows an example of how a user uploads a local JSON file into the service, encrypt it, and then view the encrypted JSON file through the postman/insomnia interface and receive the keys for decrypt the JSON file via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface. This service does not send the encrypted JSON file to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1
Output:
	2 via postman/insomnia interface and 5 via email

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/encrypt
		Input params:
			json: document in JSON format
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![imagen23](https://user-images.githubusercontent.com/39604832/46701998-2eed7d00-cbe7-11e8-82cf-aea7ee1cd573.png)
   ![imagen24](https://user-images.githubusercontent.com/39604832/46701999-2eed7d00-cbe7-11e8-8b7f-4faf490d6c18.png)

#### Encryption of entities from an Orion Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL, encrypt them, and then view the encrypted entities through the postman/insomnia interface and receive the keys for decrypt the JSON file via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface. This service does not send the encrypted entities to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1 and 2
Output:
	5 via email or postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/encrypt/ocb/local
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```
<a href="https://ibb.co/dwZ2aL"><img src="https://preview.ibb.co/kXpqUf/Encrypt-from-OCB-to-Local.png" alt="Encrypt-from-OCB-to-Local" border="0"></a>
![mail](https://user-images.githubusercontent.com/38957081/48510835-9916c480-e81a-11e8-90cb-39d5d9ca2ffa.png)

#### Encryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL or uploads a local JSON file, encrypt them/it, and then send the encrypted entities to an Orion Context Broker URL. Furthermore, the service shows the encrypted JSON file or entities through the postman/insomnia interface and the user receive the keys for decrypt the information via email. To download the encrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface.
```
Input:
	1 and 2
Output:
	5 via email or postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/encrypt/ocb
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			urlTo: http://192.168.10.193:1026/v2/entities
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![imagen26](https://user-images.githubusercontent.com/39604832/46702001-2f861380-cbe7-11e8-8fa0-c7257f67aa53.png)
   ![imagen27](https://user-images.githubusercontent.com/39604832/46702002-2f861380-cbe7-11e8-8846-c08867501548.png)

#### Decryption of a local JSON file
This process shows an example of how a user uploads a local encrypted JSON file and its corresponding key's file into the service, decrypt it, and then view the decrypted JSON file through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface. This service does not send the decrypted JSON file to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1 and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/decrypt
		Input params:
			json: document in JSON format
			key: keys sent to users email
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![imagen25](https://user-images.githubusercontent.com/39604832/46702000-2f861380-cbe7-11e8-9cf8-59a9cbed4ee8.png)

#### Decryption of entities from an Orion Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL and uploads its corresponding key's file into the service, decrypt them, and then view the decrypted entities through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface. This service does not send the decrypted entities to an Orion Context Broker URL; the service that allows this is explained below.
```
Input:
	1, 2, and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/decrypt/ocb/local
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			key : keys sent to users email
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```
<a href="https://ibb.co/e96th0"><img src="https://preview.ibb.co/fRFVUf/Decrypt-from-OCB-to-Local.png" alt="Decrypt-from-OCB-to-Local" border="0"></a>

#### Decryption of a local JSON file or entities from an Orion Context Broker URL and send them to a Context Broker URL
This process shows an example of how a user specifies entities from an Orion Context Broker URL or uploads a local JSON file with its corresponding key's file, decrypt them/it, and then send the decrypted entities to an Orion Context Broker URL. Furthermore, the service shows the decrypted JSON file or entities through the postman/insomnia interface. To download the decrypted JSON file, the user must copy the postman/insomnia response into a text file and save it with the JSON extension or download directly from the postman/insomnia interface.
```
Input:
	1, 2, and 5
Output:
	2 via postman/insomnia interface

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/decrypt/ocb
		Input params:
			urlFrom: example http://127.0.0.1:1026/v2/entities
			id: id attribute to identify the entity
			type: type attribute to identify the entity
			urlTo: http://192.168.10.193:1026/v2/entities
			key : keys sent to users email
		Headers:
			Fiware-Service:'default'
			Fiware-ServicePath:'/'
```

   ![imagen28](https://user-images.githubusercontent.com/39604832/46702004-2f861380-cbe7-11e8-8f2b-1e8d4ffd39d0.png)

#### Sign out
This service allows the user to sign out of the encryption service.
```
Input:
	None
Output:
	None

Postman/Insomnia specifications
	Http endpoint:
		URL: http://127.0.0.1:2121/logout
		Input params:
		  None
```

![imagen29](https://user-images.githubusercontent.com/39604832/46702005-2f861380-cbe7-11e8-8745-d371283036f7.png)

