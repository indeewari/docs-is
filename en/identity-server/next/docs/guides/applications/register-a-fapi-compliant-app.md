# Register a FAPI compliant application

Financial-Grade API (FAPI) is a standard that extends the OAuth and OIDC frameworks to provide enhanced security to applications. Although this is a standard initially defined for financial services, any developer that has integrated OAuth and OIDC protocols into their applications should consider making those applications FAPI compliant to incorporate the highest level of security.

## Overview

WSO2 Identity Server supports your applications to be FAPI compliant by offering the following features.

- **Conveniently create FAPI compliant applications** - Create FAPI compliant applications via the WSO2 Identity Console or via Dynamic Client Registration (DCR)

- **Support for certificate bound access tokens** - Enhance security of tokens by associating the access token with the user’s public key.

- **Support for pairwise subject identifiers** - Generate a unique identifier for a user for each client user combination so that the user cannot be tracked across multiple services based on a single identifier.

- **Support for FAPI compliant additional object validations**.
    - Mandate the authorization request object to be passed via the `request` or the `request_uri` parameter.

    - Pushed authorization requests (PAR) must send the PKCE code in the S256 code challenge method.
    
    - The request object must contain the following mandatory fields.
        - nbf
        - exp
        - scope
        - redirect_uri
        - Nonce  
    -  Request object must be signed with an algorithm allowed by FAPI standards (e.g. PS256, ES256)


- **Support for additional validations in the authorization flow**

    - If the response type of a token request is set to `code`, the `response_mode` of the request object should be set to `jwt`.

- **Support for an improved client authentication mechanism**.

    - If the client is authenticated with `private key JWT`, the client assertion must be signed using a FAPI allowed algorithm (PS256, ES256).

The following diagram illustrates how the features mentioned above combine to create a more robust authentication/authorization mechanism.

![Fapi compliant application flow]({{base_path}}/assets/img/guides/applications/fapi-compliant-apps/fapi-compliant-application.png){: style="display: block; margin: 0 auto; border: 0.3px solid lightgrey;"}

## Register the application

Follow the steps below to register a FAPI compliant application:

1. On the {{ product_name }} Console, go to **Applications**.

2. Click **New Application** and select **Standard-Based Application**.

3. Provide an application name.

4. Select **OAuth2.0 OpenID Connect** as the protocol and select **FAPI Compliant Application**.

    !!! info
        Enabling **FAPI Compliant Application** limits the application configurations to only FAPI compliant protocols. Learn how to implement all FAPI features in the [secure client-server communication](#secure-client-server-communication) section.

    ![Register a standard-based application]({{base_path}}/assets/img/guides/applications/fapi-compliant-apps/register-a-fapi-application.png){: width="600" style="display: block; margin: 0 auto; border: 0.3px solid lightgrey;"}

5. Click **Register** to complete the registration.

## Secure client-server communication

Follow the sections below to implement FAPI-compliant features to secure the communication between the client and the authorization server.

### Configure the client authentication method

The WSO2 Identity Server uses the selected client authentication method to verify the identity of the client making requests to it.

Follow the steps below to configure a FAPI-compliant client authentication method.

1. On the {{ product_name }} Console, go to **Applications**.

2. Select the created FAPI-compliant application and go to its **Protocol** tab.

3. Under **Client Authentication**, select one of the following **Client authentication methods**.

    ![Choose a fapi compliant authentication method]({{base_path}}/assets/img/guides/applications/fapi-compliant-apps/fapi-compliant-client-authentication-methods.png){: width="600" style="display: block; margin: 0 auto; border: 0.3px solid lightgrey;"}

    - **Private Key JWT** - Select this option for the client to send a client-generated JSON Web Token (JWT) signed with the private key in its authentication request. The authorization server will verify the signature with the client's public key.

        Select one of the FAPI compliant algorithms to sign the JWT under **Signing algorithm** (PS256, ES256).

        !!! info
            Learn more about private key JWT in [Implement private key JWT client authentication for OIDC]({{base_path}}/guides/authentication/oidc/private-key-jwt-client-auth/).

    - **Mutual TLS** - Select this option for the server to present its certificate to the client to verify its identity and for the client to reciprocate by sending its certificate to the server to establish a two-way trust relationship.

        Enter the domain name of the client certificate under **TLS client authentication subject domain name**.

4. Click **Update** to save the changes.

### Configure the request object

Securing the OIDC request object through signing ensures the integrity of the object. Adding a layer of encryption provides the object with an additional level of security especially with browser based flows such as the authorization code flow.

Follow the steps below to configure a FAPI-compliant request object:

1. On the {{ product_name }} Console, go to **Applications**.

2. Select the created FAPI-compliant application and go to its **Protocol** tab.

3. Select a FAPI-compliant signing algorithm under **Request object signing algorithm**.

4. Optionally, to encrypt the request object, select,
    - a FAPI-compliant asymmetric key encryption algorithm under **Request object encryption algorithm**
    - a FAPI-compliant symmetric key encryption method under **Request object encryption method**.

![Choose fapi compliant request object configurations]({{base_path}}/assets/img/guides/applications/fapi-compliant-apps/fapi-compliant-request-object-configurations.png){: width="600" style="display: block; margin: 0 auto; border: 0.3px solid lightgrey;"}

5. Click **Update** to save the changes.

### Configure the response

JWT Secured Authorization Response Mode (JARM) is a FAPI-compliant method to secure authorization responses in OIDC and OAuth 2.0 flows. If the client supports JARM, the authorization server includes the details of the response such as the authorization code and the access token in a JWT, signs it using the authorization server's public key and sends it to the client.

Follow the steps below to configure a FAPI-compliant OIDC response:

1. On the {{ product_name }} Console, go to **Applications**.

2. Select the created FAPI-compliant application and go to its **Protocol** tab.

3. Select a FAPI-compliant signing algorithm under **ID token response signing algorithm**.

![Choose fapi compliant ID token signing algorithm]({{base_path}}/assets/img/guides/applications/fapi-compliant-apps/fapi-compliant-id-token-response.png){: width="600" style="display: block; margin: 0 auto; border: 0.3px solid lightgrey;"}

4. Click **Update** to save the changes.

### Configure the subject identifier

{{product_name}} uses a subject attribute to uniquely identify the user logging into different applications. Having a single subject identifier for multiple clients poses several risks such as external entities being able to track the activity of an individual based on a single attribute.

FAPI specification strongly recommends using pairwise subject identifiers to mitigate these risks. With a pairwise subject identifier, {{product_name}} generates a unique pseudonymous ID for each user-client pair protecting the user's identity when accessing multiple applications.

Follow the steps below to configure a FAPI-compliant subject identifier:

1. On the {{ product_name }} Console, go to **Applications**.

2. Select the created FAPI-compliant application and go to its **User Attributes** tab.

3. Under **Subject type**, select **Pairwise**.

4. Enter a **Sector Identifier URI**.

    !!! info
        The sector identifier URI is used to group clients belonging to the same security domain so that the same pairwise identifier is used for a given user accessing these clients.

    ![Enter a suctor identifier for pairwise subject identifier]({{base_path}}/assets/img/guides/applications/fapi-compliant-apps/fapi-compliant-subject-identifier.png){: width="600" style="display: block; margin: 0 auto; border: 0.3px solid lightgrey;"}

4. Click **Update** to save the changes.

### Configure risk levels

Applications can achieve a higher level of security by defining risk levels for authentication. The authorization server can then assess the risk associated with a given activity or user and dynamically adjust the strength of authentication.

 {{product_name}} enables you to define these risk levels and adjust authentication steps dynamically using Authentication Context Reference (ACR) based adaptive authentication. <!--add-the-link -->





