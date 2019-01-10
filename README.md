# Demo of Authorization Services on Red Hat Single Sign-On

## Setup

### Software Downloads:
* Red Hat Single Sign-On 7.2
* Red Hat Single Sign-On 7.2.5 Server Patch
* Red Hat Single Sign-On 7.2.5 Client Adapter for JBoss EAP 7
* Red Hat JBoss Enterprise Application Platform 7.1.0

### Red Hat Single Sign-On installation and configuration

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





