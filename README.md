Install cauth for repoXplorer
=============================

Generate key pair for cauth
---------------------------

openssl genrsa -out privkey.pem 2048
openssl rsa -in privkey.pem -out pubkey.pem -pubout

Run the ansible playbook
------------------------

- Copy keypair files to files/
- Set the inventory
- ansible-playbook -i inventory.yaml -e @defaults/main.yaml cauth.yaml  

For the repoxplorer virtualhost:

Now add some conf for the apache repoxplorer.conf
-------------------------------------------------

- Add in repoxplorer.conf

RewriteEngine  on
RewriteRule    "^/$"  "/index.html"  [R]

<LocationMatch "/home.html">
    Order Allow,Deny
    Allow from all

    AuthType mod_auth_pubtkt
    TKTAuthFakeBasicAuth on
    TKTAuthLoginURL /auth/login
    TKTAuthDebug 1
    require valid-user
</LocationMatch>


<Location "/">
    RequestHeader unset X-Remote-User
    <If "%{HTTP_COOKIE} =~ /auth_pubtkt=.*/">
        AuthType mod_auth_pubtkt
        TKTAuthLoginURL /auth/login
        TKTAuthFakeBasicAuth on
        TKTAuthDebug 1
        require valid-user
        RequestHeader set X-Remote-User %{REMOTE_USER}s
    </If>
</Location>
