Check SAP exposure:
go to https://rndwww.nce.amadeus.net/si-viewer/?e=local:PDT_current.db3&o=xml_svc&v=XML_TARCPQ_%2A_%2A#tab1|svc_pub_xml__xml_svc:edgq0vk1dnjkmca1ad4ngmu39x0m8ka99rx36z3kethpwrbdcmjk6gar9n65yn21a91n0maz59fjm, 

XML services

then search your one, click on it and check the SAP section.
Check that the SAP: 1ASIXSCOADMIN is in the list 


Check in Connector:
Check here: https://rndwww.nce.amadeus.net/git/projects/CNT/repos/fwk_access/browse/connectors-verbhandler-core/src/main/resources/META-INF/verbs.xml
if the verb is not there, then open WO like this one: 25345782  http://aproach.muc.amadeus.net/NotesLink/nl?RNID=25345782


bear exposure of multiple XSDs linked together example: https://rndwww.nce.amadeus.net/git/projects/SECO/repos/bear/pull-requests/246/diff#bear-jaxb/src/main/resources/bindings/security.xjb
