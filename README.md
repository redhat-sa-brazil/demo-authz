# Demo of Authorization Services on Red Hat Single Sign-On

## Slide Deck

https://docs.google.com/presentation/d/1FxKu6pZAFMumdpagicyV0JQrscxA2dKqZelibO1Czhw/edit

## Setup

### Software Downloads:
* Red Hat Single Sign-On 7.2 (rh-sso-7.2.0.zip)
* Red Hat Single Sign-On 7.2.5 Server Patch (rh-sso-7.2.5-patch.zip)
* Red Hat Single Sign-On 7.2.5 Client Adapter for JBoss EAP 7 (rh-sso-7.2.5-eap7-adapter.zip)
* Red Hat JBoss Enterprise Application Platform 7.1.0 (jboss-eap-7.1.0.zip)

### Red Hat Single Sign-On and JBoss EAP installation and configuration

```bash
unzip rh-sso-7.2.0.zip

```

**1** - Adding admin user:

```bash
./rh-sso-7.2/bin/add-user.sh
```

**2** - Appling Patch 7.2.5

```bash
./rh-sso-7.2/bin/standalone.sh &

./rh-sso-7.2/bin/jboss-cli.sh --connect
  patch apply rh-sso-7.2.5-patch.zip
```

**3** - Installing JBoss EAP 7.1

```bash
unzip jboss-eap-7.1.0.zip

```

**4** - Adding admin user:

```bash
./jboss-eap-7.1/bin/add-user.sh
```

**5** - Installing RH-SSO Adapter for JBoss EAP 7.1

```bash
cd jboss-eap-7.1
unzip rh-sso-7.2.5-eap7-adapter.zip
./bin/standalone.sh &

./bin/jboss-cli.sh --connect --file=./bin/adapter-install.cli
```

## Preparing applications and dependencies

```bash
git clone https://github.com/keycloak/keycloak
git checkout tags/3.4.3.Final
mvn clean package -f keycloak/testsuite/integration-arquillian/test-apps/photoz/pom.xml
cp keycloak/testsuite/integration-arquillian/test-apps/photoz/photoz-authz-policy/target/photoz-authz-policy-3.4.3.Final.jar rh-sso-7.2/standalone/deployments
cp keycloak/testsuite/integration-arquillian/test-apps/photoz/photoz-html5-client/target/photoz-html5-client.war jboss-eap-7.1/standalone/deployments
cp keycloak/testsuite/integration-arquillian/test-apps/photoz/photoz-restful-api/target/photoz-restful-api.war jboss-eap-7.1/standalone/deployments
```

## Demo Preparation

### Starting Red Hat Single Sign-On

```bash
./rh-sso-7.2/standalone.sh -b 0.0.0.0 -bmanagement 0.0.0.0 -bunsecure 0.0.0.0 -Djboss.socket.binding.port-offset=1 -Dkeycloak.profile=preview
```

**1** - Open Red Hat Single Sign-On and enter the admin username and password (or create an admin user) on http://localhost:8081/auth/admin/.

**2** - Click on Add-Realm and add a realm `photoz` importing the file `photoz-realm.json`

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/add-realm.png?raw=true)

**3** - Open the tab Authorization of client `photoz-restful-api` and import the file `photoz-restful-api-authz-service.json`

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/import-authz.png?raw=true)

**4** - Starting Red Hat JBoss EAP with deployed applications

```bash
./jboss-eap-7.1/standalone.sh -b 0.0.0.0 -bmanagement 0.0.0.0 -bunsecure 0.0.0.0
```

## Demo Presentation

### Applications
* photoz-html5-client
* photoz-restful-api

### Users/passwords
* alice/alice
* jdoe/jdoe
* admin/admin

**1** - Access Red Hat Single Sign-On in http://localhost:8081/auth/admin

**2** - Click on `Clients` in `photoz` realm. Open  **photoz-restful-api** client configuration click on **Authorization** tab and show the Settings in **Resources**, **Authorization Scopes**, **Policies** and **Permissions**. Explain each one if possible.

**3** - Access the `photoz-html5-client` application in http://localhost:8080/photoz-html5-client

**4** - Login with user **Alice**

**5** - Consent the use of your data from photoz-restful-api by photoz-html-client

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/consent-screen.png?raw=true)

**6** - Click on `Show Requesting Party Token` to see the authorization permissions on Token from Album URL that you just requested on main page

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/party-token.png?raw=true)

**7** - Now click on `My Profile`. Then click back on `Show Requesting Party Token` to check you received a new `profile:view` permission in RPT

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/profile-view.png?raw=true)

**8** - Click back on browser then create an album `alices album`. This will be the album #1

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/create-album.png?raw=true)

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/alices-album.png?raw=true)

**9** - Click on `Sign Out` and log-in with user `jdoe`

**10** - Click on Yes to authorize `photoz-html-client` on consent screen

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/grant-jdoe.png?raw=true)

**11** - Create an album `jdoes album`. This will be the album #2

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/jdoes-album.png?raw=true)

**12** - Now click on `Sign Out` again and log-in with user `alice` to see the albuns

**13** - Log-in with admin in Red Hat Single Sign-On and go to `Clients -> photoz-restful-api -> Authorization -> Permissions` and edit the permission `Album Resource Permission`. Remove the Policy `Any User Policy`

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/remove-album-permission.png?raw=true)

**14** - Refresh the page in `photoz-html5-client` to check you don't have access anymore

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/access-denied1.png?raw=true)

**15** - Update the URL in browser to guarantee you don't have access

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/access-denied2.png?raw=true)

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/access-denied3.png?raw=true)

**16** - Now on RH-SSO edit the Permission `Album Resource Permission`. Add the Policy `Only From @keycloak.org or Admin` and change the Decision Strategy to `Unanimous`

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/admin-album.png?raw=true)

**17** - Refresh the page in `photoz-html5-client` to confirm Alice still doesn't have access.

**18** - Now log-out and log-in again in `photoz-html5-client` with user `admin` and click on `All Albuns` and confirm you can see all album resources.

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/all-albums.png?raw=true)

**19** - Log-in again with user `jdoe` and access the jdoes album `photoz-html5-client/#/album/2` to confirm you can see the album resource.

**20** - Now go to RH-SSO and create a Policy based on time. Create a `negative policy` that will deny the access of jdoes album for 2 minutes. 
         As the example below in minutes 42 and 43 the access wil be denied.

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/time-based.png?raw=true)

**21** - Then create a new permission based on `jdoes album` and add the time policy you just created

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/jdoes-album-time-based.png?raw=true)

**22** - Refresh the page to confirm that you cannot access album resource in the specific minutes you have defined.

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/access-denied-admin.png?raw=true)

### Cleanup:
* Delete photoz Realm in RH-SSO
* Delete the content of folders `tmp` and `data` in JBoss EAP 7.1 and restart JBoss



