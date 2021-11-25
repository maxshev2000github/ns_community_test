sfdx force:community:create --name "meservice" --templatename "Customer Service" --urlpathprefix options
sfdx force:org:create -f config/project-scratch-def.json -a scr2 -s
sfdx force:source:deploy -p unpackaged
sfdx force:mdapi:retrieve -r ./mdapi-source/ -u ISVPack -k ./package.xml
unzip ./mdapi-source/unpackaged.zip -d ./mdapi-source/community
sfdx force:mdapi:convert -r mdapi-source/community/unpackaged -d unpackaged
sfdx force:mdapi:convert -r mdapi-source/community
sfdx force:source:convert \
    -d mdapi-source/community \
    -n "Food-Bank"
sfdx force:mdapi:retrieve -u ISVPack -p "Food-Bank" -r mdapi-source/package -w 10 -s
# Notes
## retrieve portal metadata
sfdx force:mdapi:retrieve -r ./mdapi-source/ -u ISVPack -k ./manifest/package.xml
## unzip
unzip ./mdapi-source/unpackaged.zip -d ./mdapi-source/community
## convert to unpackaged directory
sfdx force:mdapi:convert -r mdapi-source/community -d unpackaged
## create scratch org
sfdx force:org:create -f config\\project-scratch-def.json -a scr2 -s
## create empty community
sfdx force:community:create --name "meservice" --templatename "Customer Service" --urlpathprefix options
## open scratch org
sfdx force:org:open
## push metadata to org
sfdx force:source:push

STATE     FULL NAME  TYPE        PROJECT PATH
────────  ─────────  ──────────  ───────────────────────────────────────────────────────────
Conflict  meservice  Network     unpackaged\main\default\networks\meservice.network-meta.xml
Conflict  Admin      Profile     unpackaged\main\default\profiles\Admin.profile-meta.xml
Conflict  meservice  CustomSite  unpackaged\main\default\sites\meservice.site-meta.xml
ERROR running force:source:push:  Source conflict(s) detected.

## force push
sfdx force:source:push -f

$ sfdx force:source:push -f
*** Deploying with SOAP ***
Job ID | 0Af1D00001f9XnbSAE
SOURCE PROGRESS | ████████████████████████████████░░░░░░░░ | 4/5 Components
TYPE   PROJECT PATH                                                 PROBLEM
─────  ───────────────────────────────────────────────────────────  ──────────────────────────────────────────────────────────────────────────────────────────────────────────
Error  unpackaged\main\default\networks\meservice.network-meta.xml  In field: welcomeEmailTemplate - no EmailTemplate named unfiled$public/CommunityWelcomeEmailTemplate found
ERROR running force:source:push:  Push failed. 

## deploy with source:deploy
sfdx force:source:deploy -p unpackaged

$ sfdx force:source:deploy -p unpackaged
*** Deploying with SOAP API ***
ERROR running force:source:deploy:  C:\Users\user\Desktop\sfdx-workshop\ns_community_test\unpackaged\main\default\sites\meservice.site-meta.xml: Expected source files for type 'SiteDotCom'

## deploy with metadata api
sfdx force:mdapi:deploy -d mdapi-source\\community\\unpackaged -w 5

*** Deploying with SOAP ***
Job ID | 0Af1D00001f9kWXSAY
MDAPI PROGRESS | ████████████████████████████████░░░░░░░░ | 4/5 Components

=== Component Failures [1]
TYPE   FILE                                   NAME       PROBLEM
─────  ─────────────────────────────────────  ─────────  ──────────────────────────────────────────────────────────────────────────────────────────────────────────
Error  unpackaged/networks/meservice.network  meservice  In field: welcomeEmailTemplate - no EmailTemplate named unfiled$public/CommunityWelcomeEmailTemplate found

ERROR running force:mdapi:deploy:  The metadata deploy operation failed.


## Deploy to Package Org LWC component
sfdx force:source:deploy -p force-app -u ISVPack

## retrieve package metadata

sfdx force:mdapi:retrieve -u ISVPack -p "Food-Bank" -r mdapi-source/package -w 10 -s

## unzip package

unzip ./mdapi-source/package/unpackaged.zip -d ./mdapi-source/package

## convert pack to source format

sfdx force:mdapi:convert -r mdapi-source/package -d force-app

## create scartch org
sfdx force:org:create -f config\\project-scratch-def.json -a scr3 -s
## push to new scratch org
sfdx force:source:push
## create community from template
sfdx force:community:create --name "meservice" --templatename "meservice" --urlpathprefix options
## open community
sfdx force:org:open -p _ui/networks/setup/SetupNetworksPage