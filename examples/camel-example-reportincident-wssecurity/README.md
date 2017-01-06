# Report Incident Example

### Introduction

An example based on real life use case for reporting incidents using webservice
that are transformed and send as emails to a backing system. WS-security has been
implemented. So, the user must generates a SOAP envelope containing a SOAP header
with wsse xml tag. A simple property map has been created containing user and password.
We use Apache CXF WSS4JInterceptor to get the user/password and timestamp and authenticate
the user using the WSS4J callback

### Build

You will need to compile this example first:

	mvn install

Remarks:
- During the compilation phase, a unit test will be performed, this unit test simulates the
  communication between a client calling the web services exposed by our camel/cxf routes. During the call,
  the user "charles" is used to authenticate the web service call and the SOAP message created can be
  retrieved from log file `target/camel-example-reportincident-wssecurity.log`
- A mock SMTP server is used during unit test
- In Eclipse, I have used the following option when starting the junit test case. This option tells
  CXF that it must use log4j : `-Dorg.apache.cxf.Logger=org.apache.cxf.common.logging.Log4jLogger`

### Run

To run the example on Apache Karaf 4.x

#### Step 1: launch the server

	karaf / karaf.bat

  For Karaf: edit the file jre.properties to add the following packages to be exported
  jre-1.6=, \
 com.sun.org.apache.xerces.internal.dom, \
 com.sun.org.apache.xerces.internal.jaxp, \

 They are required by the following bundle : org.apache.servicemix.bundles/org.apache.servicemix.bundles.saaj-impl/1.3.2_1

#### Step 2: Add features required

	feature:repo-add mvn:org.apache.camel.karaf/apache-camel/${version}/xml/features
	feature:install http
	feature:install camel
	feature:install camel-cxf
	feature:install camel-mail
	feature:install camel-velocity
        feature:install cxf-bindings-corba
        feature:install cxf-transports-jms
        feature:install cxf-ws-security

  remark: As the camel route sends email to a SMTP server, you must configure a user/password in your favorite
          SMTP Server (James by example). User = someone and password = secret

#### Step 3: Deploy our example

	install -s mvn:org.apache.camel/camel-example-reportincident-wssecurity/${project.version}

#### Step 4: Verify that your service is available using in the browser the following url

	http://localhost:9081/camel-example-reportincident/webservices/incident?wsdl

<http://localhost:9081/camel-example-reportincident/webservices/incident?wsdl>

#### Step 5: Start SOAPUI (2.x)
  Create a new project called `camel-example-reportincident-wssecurity`
  Point to the following url : <http://localhost:9081/camel-example-reportincident/webservices/incident?wsdl>
  Open the request 1 (under camel-example-reportincident-wssecurity --> ReportIncidentBinding --> ReportIncident) and copy/paste the SOAP
  message generated by the unit test (don't copy the payload below as it's credentials expired minutes after we pasted them into this readme!)

  ex :

	2010-07-14 09:57:54,403 [main           ] INFO  LoggingOutInterceptor          - Outbound Message
	---
	ID: 1
	Address: http://localhost:9081/camel-example-reportincident/webservices/incident
	Encoding: UTF-8
	Content-Type: text/xml
	Headers: {SOAPAction=["http://reportincident.example.camel.apache.org/ReportIncident"], Accept=[*/*]}
	Payload: <soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"><soap:Header><wsse:Security xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" soap:mustUnderstand="1"><wsu:Timestamp xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" wsu:Id="Timestamp-2"><wsu:Created>2010-07-14T07:57:54.387Z</wsu:Created><wsu:Expires>2010-07-14T08:02:54.387Z</wsu:Expires></wsu:Timestamp><wsse:UsernameToken xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" wsu:Id="UsernameToken-1"><wsse:Username>charles</wsse:Username><wsse:Password Type="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordDigest">0U5uXRYukYG5PF82gsmncH+yWEE=</wsse:Password><wsse:Nonce EncodingType="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-soap-message-security-1.0#Base64Binary">/Ka8O+F8cyufohiJFp8wjA==</wsse:Nonce><wsu:Created>2010-07-14T07:57:54.387Z</wsu:Created></wsse:UsernameToken></wsse:Security></soap:Header><soap:Body><ns2:inputReportIncident xmlns:ns2="http://reportincident.example.camel.apache.org"><incidentId>123</incidentId><incidentDate>2008-08-18</incidentDate><givenName>Claus</givenName><familyName>Ibsen</familyName><summary>Bla</summary><details>Bla bla</details><email>davsclaus@apache.org</email><phone>0045 2962 7576</phone></ns2:inputReportIncident></soap:Body></soap:Envelope>
	--------------
	2010-07-14 09:57:54,403 [main           ] DEBUG HTTPConduit                    - Sending POST Message with Headers to http://localhost:9080/camel-example-reportincident/webservices/incident Conduit :{http://reportincident.example.camel.apache.org}ReportIncidentEndpointPort.http-conduit

  --> and the message formatted that you copy in SOAPUI

		<?xml version="1.0" encoding="UTF-8"?>
		<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
			<soap:Header>
				<wsse:Security xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" soap:mustUnderstand="1">
					<wsu:Timestamp xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" wsu:Id="Timestamp-2">
						<wsu:Created>2010-07-14T09:40:29.637Z</wsu:Created>
						<wsu:Expires>2010-07-14T09:45:29.637Z</wsu:Expires>
					</wsu:Timestamp>
					<wsse:UsernameToken xmlns:wsse="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:wsu="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd" wsu:Id="UsernameToken-1">
						<wsse:Username>charles</wsse:Username>
						<wsse:Password Type="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-username-token-profile-1.0#PasswordDigest">TVzWGxNvhlixNVWol8poD9DHxl8=</wsse:Password>
						<wsse:Nonce EncodingType="http://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-soap-message-security-1.0#Base64Binary">WsMNSm/C4dzdPS3OhUi94Q==</wsse:Nonce>
						<wsu:Created>2010-07-14T09:40:29.637Z</wsu:Created>
					</wsse:UsernameToken>
				</wsse:Security>
			</soap:Header>
			<soap:Body>
				<ns2:inputReportIncident xmlns:ns2="http://reportincident.example.camel.apache.org">
					<incidentId>111</incidentId>
					<incidentDate>2010-07-14</incidentDate>
					<givenName>Charles</givenName>
					<familyName>Moulliard</familyName>
					<summary>Bla</summary>
					<details>Bla bla</details>
					<email>cmoulliard@apache.org</email>
					<phone>0011 22 33 44</phone>
				</ns2:inputReportIncident>
			</soap:Body>
		</soap:Envelope>


 You can use another user: james, claus and retry.

#### Step 6: Check email
 Check through a POP request that a message has been published in the mailbox of someone (email address : incident@mycompany.com)

### Documentation

This example is documented at
  <http://camel.apache.org/tutorial-osgi-camel-part1.html>

### Forum, Help, etc

If you hit an problems please let us know on the Camel Forums
	<http://camel.apache.org/discussion-forums.html>

Please help us make Apache Camel better - we appreciate any feedback you may
have.  Enjoy!