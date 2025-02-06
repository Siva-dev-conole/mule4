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


