# Demo of Authorization Services on Red Hat Single Sign-On

## Presentation

https://docs.google.com/presentation/d/1FxKu6pZAFMumdpagicyV0JQrscxA2dKqZelibO1Czhw

## Setup

### Software Downloads:
* Red Hat Single Sign-On 7.2
* Red Hat Single Sign-On 7.2.5 Server Patch
* Red Hat Single Sign-On 7.2.5 Client Adapter for JBoss EAP 7
* Red Hat JBoss Enterprise Application Platform 7.1.0

### Red Hat Single Sign-On and JBoss EAP installation and configuration

```bash
unzip rh-sso-7.2.0.zip
```

Adding admin user:

```bash
./rh-sso-7.2/bin/add-user.sh
```

Appling Patch 7.2.5

```bash
./rh-sso-7.2/bin/standalone.sh &

./rh-sso-7.2/bin/jboss-cli.sh --connect
  patch apply rh-sso-7.2.5-patch.zip
```

Installing JBoss EAP 7.1

```bash
unzip jboss-eap-7.1.0.zip
```

Adding admin user:

```bash
./jboss-eap-7.1/bin/add-user.sh
```

Installing RH-SSO Adapter for JBoss EAP 7.1

```bash
cd jboss-eap-7.1
./jboss-eap-7.1/bin/standalone.sh &

./jboss-eap-7.1/bin/jboss-cli.sh --connect --file=./jboss-eap-7.1/bin/adapter-install.cli
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

## Starting Red Hat Single Sign-On

```bash
./rh-sso-7.2/standalone.sh -b 0.0.0.0 -bmanagement 0.0.0.0 -bunsecure 0.0.0.0 -Djboss.socket.binding.port-offset=1 -Dkeycloak.profile=preview
```

## Starting Red Hat JBoss EAP

```bash
./jboss-eap-7.1/standalone.sh -b 0.0.0.0 -bmanagement 0.0.0.0 -bunsecure 0.0.0.0
```

# Demo Presentation

1 - Open Red Hat Single Sign-On and enter the admin username and password (or create an admin user) on http://localhost:8081/auth/admin/.

2 - Import the realm 'realm-photoz.json'

![](https://github.com/redhat-sa-brazil/demo-authz/blob/master/pictures/Import-json.png?raw=true)





