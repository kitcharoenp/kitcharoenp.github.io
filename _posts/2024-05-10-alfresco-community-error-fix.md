---
layout : post
title : "Alfresco Community 23.x Error Fixed"
categories : [alfreco]
published : true
---
* *Cannot find Alfresco Repository on this server. (Does this application have access to alfresco-global.properties? Does this application have cross-context permissions?)*

   * Check `alfresco-global.properties` file locate at `"$ALF_INSTALLATION_DIR/tomcat/shared/classes/alfresco-global.properties":`


   *  ERROR: relation "alf_prop_string_value" does not exist


   *  ERROR: relation "alf_prop_value_seq" does not exist

   *  ERROR: relation "alf_prop_root_seq" does not exist

   *  ERROR: relation "alf_prop_serializable_value_seq" does not exist

   *  ERROR: relation "alf_prop_link" does not exist

 


   CREATE SEQUENCE alf_prop_class_seq START WITH 1 INCREMENT BY 1;
CREATE TABLE alf_prop_class
(
   id INT8 NOT NULL,
   java_class_name VARCHAR(255) NOT NULL,
   java_class_name_short VARCHAR(32) NOT NULL,
   java_class_name_crc INT8 NOT NULL,   
   PRIMARY KEY (id)
);