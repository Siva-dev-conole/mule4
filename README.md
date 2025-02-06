# mule4
mule4 dataweave examples


First Approach
----------------------
%dw 2.0
output application/json
var params = {
    "value1": "123",
    "value2": "xyz",
    "value3": "abc"
}
var queryStrng = "INSERT INTO table_nake (column1, column2, column3) VALUES (:value1, :value2, :value3);"

---
 "INSERT INTO table_nake (column1, column2, column3) VALUES " ++ ((params filterObject ((!isEmpty( $ ))) mapObject ({ myData :"'"++($ as String )++"'"})).*myData joinBy " , ") ++ ");"




output:

"INSERT INTO table_nake (column1, column2, column3) VALUES '123' , 'xyz' , 'abc');"



Second Approach
----------------------

%dw 2.0
output application/json
var params = {
    "value1": "123",
    "value2": "xyz",
    "value3": "abc"
}
var queryStrng = "INSERT INTO table_nake (column1, column2, column3) VALUES (:value1, :value2, :value3);"
var replaceKeys=((params filterObject ((!isEmpty( $ ))) mapObject ({ myData :":"++($$ as String )})).*myData joinBy ", ")
var replaceValues=((params filterObject ((!isEmpty( $ ))) mapObject ({ myData :"'"++($ as String )++"'"})).*myData joinBy " , ")
---
  
queryStrng replace replaceKeys with replaceValues


output:

"INSERT INTO table_nake (column1, column2, column3) VALUES ('123' , 'xyz' , 'abc');"


Third Approach

--------------------


<!--flow name="sampleFlow" doc:id="ab82c311-1b99-4b64-a0fd-2100d4923639" >
		<scheduler doc:name="Scheduler" doc:id="aa5f7108-46d7-4e43-8669-ab2a2b6d0557" >
			<scheduling-strategy >
				<fixed-frequency timeUnit="MINUTES"/>
			</scheduling-strategy>
		</scheduler>
		<set-variable value='#["INSERT INTO table_nake (column1, column2, column3) VALUES (:value1, :value2, :value3);"]' doc:name="query" doc:id="2c20a44f-5c98-4f1b-88a5-1bf8ff13ec29" variableName="queryString"/>
		<set-variable value='#[{&#10;    "value1": "123",&#10;    "value2": "xyz",&#10;    "value3": "abc"&#10;}]' doc:name="input" doc:id="6987a580-d527-4673-a75b-1a123ad45a68" variableName="varinput"/>
		<foreach doc:name="For Each" doc:id="f3bcded3-2fb5-4c49-bf6c-f6b386d0e88b" collection='#[keysOf(vars.varinput)]'>
			<logger level="INFO" doc:name="Logger" doc:id="0cca225a-1e6f-47dd-a154-5e2a4a97161b" />
			<set-variable value='#[%dw 2.0&#10;import * from dw::core::Strings&#10;output application/json&#10;---&#10;&#10;replaceAll(vars.queryString,(":" ++ payload),vars.varinput[payload])]' doc:name="Set Variable" doc:id="1baa1535-9a9f-4c6d-84f5-afba6ceb66ce" variableName="queryString"/>
		</foreach>
		<ee:transform doc:name="Transform Message" doc:id="96fd1554-1e57-462b-8e13-efa816664c64" >
			<ee:message >
				<ee:set-payload ><![CDATA[%dw 2.0
output application/json
---
{"query":vars.queryString
}]]></ee:set-payload>
			</ee:message>
		</ee:transform>
		<logger level="INFO" doc:name="Logger" doc:id="0c5462df-a394-4b47-84ec-6436366fa8e4" message="#[payload]"/>
	</flow-->




