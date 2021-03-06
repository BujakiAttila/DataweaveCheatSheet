# Dataweave Cheatsheet (v1.0)

Reference: https://docs.mulesoft.com/mule-user-guide/v/3.9/dataweave

`$$`: key / index

`$`: value

`@`: Create or reference XML attribute 

`%output application/dw`: [Outputs the cannonical datamodel of DataWeave](https://docs.mulesoft.com/mule-user-guide/v/3.8/dataweave-reference-documentation#example-transformation-to-dataweave)

__Object:__

~~~~
{
  key: "value"
}
~~~~

__Array:__
~~~~
%dw 1.0
%output application/json
---
[ "elem1", "elem2", "elem3" ]
~~~~

__Map operator:__

~~~~
%dw 1.0
%output application/json
---
shirts: payload map {
        size: upper $.size,
        description: $.description,
        count: $.count
}
~~~~

__Constants and functions (%var):__

~~~~
%dw 1.0
%var prefix = 'The destination is: '
%var append = (param1, param2) ->  param1 ++ param2
%var createFlightCodeElement = (code) -> flightCode: code
%var createDestinationCodeElement = (dest) -> destinationCode: dest
%output application/xml
---
flights: {(payload map {
	flight @(flightCode: $.code):
	{	
		destination: append(prefix, $.destination),
        code: createFlightCodeElement($.code),
        dest: createDestinationCodeElement($.destination)
	}
 })
}
~~~~

<table>
<tr>
<th colspan=3>

__XML attribute in output__

<tr>
<th>
Input
<th>
Transformation
<th>
Output
<tr>
<td>
  
~~~~
[{
	"ID": 1,
	"code": "ER38sd",
	"destination": "SFO",
	"plane": {
		"type": "Boeing 737",
		"totalSeats": 150
	}
}, {
	"ID": 2,
	"code": "ER45if",
	"destination": "LAX",
	"plane": {
		"type": "Boeing 777",
		"totalSeats": 300
	}
}]
~~~~

<td>
  
~~~~
%dw 1.0
%output application/xml
---
flights: {(payload map {
	flight @(flightCode: $.code):
	{	
		destination: $.destination
	}
 })
}
~~~~
  
<td>
  
  
~~~~
<?xml version='1.0' encoding='windows-1252'?>
<flights>
  <flight flightCode="ER38sd">
    <destination>SFO</destination>
  </flight>
  <flight flightCode="ER45if">
    <destination>LAX</destination>
  </flight>
</flights>
~~~~
  

<tr>
<th colspan=3>

__Item index in the output ("$$")__

<tr>
<th>
Input
<th>
Transformation
<th>
Output
<tr>
<td>
  
~~~~
[{
	"ID": 1,
	"code": "ER38sd",
	"destination": "SFO",
	"plane": {
		"type": "Boeing 737",
		"totalSeats": 150
	}
}, {
	"ID": 2,
	"code": "ER45if",
	"destination": "LAX",
	"plane": {
		"type": "Boeing 777",
		"totalSeats": 300
	}
}]
~~~~

<td>
  
~~~~
%dw 1.0
%output application/xml
---
flights: {(payload map {
	flight @(index: $$):
	{	
		code: $.code
	}
})
}
~~~~
  
<td>
  
  
~~~~
<?xml version='1.0' encoding='windows-1252'?>
<flights>
  <flight index="0">
    <code>ER38sd</code>
  </flight>
  <flight index="1">
    <code>ER45if</code>
  </flight>
</flights>
~~~~
  
</table>
