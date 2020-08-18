# JasperReportsServer_LDAP
applicationContext and settings for external auth Jasper Server with LDAP

## Configuration

### Active Directory settings

```
	<bean id="ldapAuthPopulator" 
		  class="com.jaspersoft.jasperserver.api.security.externalAuth.wrappers.spring.ldap.JSDefaultLdapAuthoritiesPopulator">
		<constructor-arg index="0"><ref local="ldapContextSource"/></constructor-arg>
		<!-- AFI pokud bychom chtěli omezit jen na vybranou skupinu (&amp;(objectClass=user)(cn={0})(memberOf=CN=JasperAccessGroup,CN=Roles,DC=domain,DC=company,DC=eu ))-->
		<!-- Pokud nechceme toto nastavení, stačí mít nastaveno: DC=czech,DC=company,DC=eu -->
		<constructor-arg index="1"><value>DC=domain,DC=company,DC=eu</value></constructor-arg>                 
		<property name="groupRoleAttribute" value="cn"/> <!-- CN - objekt AD, ponechat bez změny -->
		<property name="groupSearchFilter" value="(&amp;(objectCategory=group)(member={0}))"/> <!-- vyčítá všechny skupiny, kterým je uživatel členem. V opačném případě nastavit filtr na index 1 tj: (uid={1}) -->
		<property name="searchSubtree" value="true"/> <!-- prohledává podřízené objekty -->
		<!-- Can setup additional external default roles here  <property name="defaultRole" value="LDAP"/> -->
		<property name="defaultRole" value="ROLE_USER"/> <!-- výchozí skupina pro uživatele -->
		<property name="convertToUpperCase" value="false"/> <!-- konverze na velké zakázána -->
	</bean>
```

### userSearch bean
```
        <constructor-arg index="0">
            <value>DC=domain,DC=company,DC=eu</value>
        </constructor-arg>
        <constructor-arg index="1">
            <!-- afi <value>(uid={0})</value> -->
			      <value>(sAMAccountName={0})</value> <!-- ověřování to, co zadal uživatel při přihlašování do jasperu -->
        </constructor-arg>
 ```       

### Role mapping
```
        <property name="organizationRoleMap">
            <map>				
				        <entry>
                    <key>
                        <value>ROLE_Jasper_Admin</value> <!-- AD role-->
                    </key>
                    <value>ROLE_ADMINISTRATOR</value> <!-- jasper role-->
                </entry>
								<entry>
                    <key>
                        <value>ROLE_Jasper_User_Department</value>
                    </key>
                    <value>ROLE_USER_DEPARTMENT</value>
                </entry>
            </map>
        </property>
		
		<!-- pouze tyto povolené skupiny mají přístup to jasperu -->
		<property name="permittedRolesRegex">
			<list>	
				<value>ROLE_Jasper_Admin</value>
				<value>ROLE_Jasper_User_Department</value>
			</list>
		</property>
```
