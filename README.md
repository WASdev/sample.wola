sample.wola
===========

Samples showing the use of WebSphere Optimized Local Adaptors (WOLA)
in the WebSphere Application Server for z/OS Liberty profile.

* BBOACPLT - Assembler sample source for CICS PLT initialization routine 
             that shows how to enable the OLA TRUE during CICS
             startup.
* BBOACPL2 - Assembler sample source for CICS PLT initialization routine 
             that shows how to get OLA INITPARMS from CICS startup parms
             and issue BBOC STRT_SRVR during CICS startup.
* BBOACPL3 - Assembler sample source for CICS PLT initialization routine 
             that shows how to pass multiple BBOC commands to BBOACNTL
             during CICS startup.
* CSDUPDAT - CICS DFHCSDUP utility job that defines all the resource
             definitions needed for OLA under CICS.  There are two
             sample CSDUPDAT jobs - one for product files and one for
             sample files.
* DFHPLTOL - JCL/source for assembling a sample PLT with the OLA
             enable TRUE exit program, BBOACPLT and the OLA
             BBOC command processor PLT, BBOACPL2
* OLABATCH - JCL to run one of the samples as a batch job. Ensure this runs
             on the same LPAR that WebSphere Application for z/OS
             Liberty profile is running
* OLACB01 -  JCL/source for CICS link to sample Cobol program that uses
             a commarea. This is a sample target program when using the
             OLA CICS Link Server. It echoes back the sent message.
* OLACB02 -  JCL/source for CICS link to sample Cobol program that uses
             a container. This is a sample target program when using the
             OLA CICS Link Server. It echoes back the sent message.
* OLACB03 -  JCL/source for CICS sample Cobol program that demonstrates
             how to make a CICS task into an OLA server using the Host
             Service API
* OLACB04 -  JCL/source for CICS sample Cobol program that demonstrates
             how to make a CICS task into an OLA server using the Receive
             Request and Get Data APIs
* OLACB05 -  JCL/source for CICS sample Cobol program that demonstrates
             how to use the APIs to Register, Get a Connection, call an
             EJB with Send Request, Get the response with Get Data, release
             the connection with Connection Release and Unregister
* OLACB06 -  JCL/source for CICS sample Cobol program that demonstrates
             how to use the APIs to Register, call an EJB with Invoke and
             Unregister
* OLACB10 -  JCL/source for CICS sample Cobol program that uses multiple
             containers to pass data to CICS from an EJB.  This is a sample
             target program when using the OLA CICS Link Server.
* OLACB11 -  JCL/source for CICS sample Cobol program that uses multiple
             containers to pass data to CICS from an EJB.  This is a sample
             target program when using the OLA CICS Link Server.  The data
             is modified inside the target program.
* OLACB12 -  JCL/source for CICS sample Cobol program that uses multiple
             containers to pass data to CICS from an EJB.  This is a sample
             target program when using the OLA CICS Link Server.  The data
             is deleted inside the target program
* OLACC01 -  JCL/source for C program that does Register/Invoke/Unregister.
             This can be run under batch/USS/CICS.
* OLACC02 -  JCL/source for C program that does Host Service/Send Request/
             Send Response/Get Data API calls. This program essentially
             invoke itself, calling in to an EJB that then calls back to
             this program. This can be run under batch/USS/CICS.
* OLACC10 -  JCL/source for a CICS sample C program that uses multiple
             containers to pass data to CICS from an EJB.  This is a sample
             target program when using the OLA CICS Link Server
* OLAMAP -   JCL/source for a CICS BMS screen map definition. This is a
             3270 test drivign screen for passing requests to and from
             WebSphere Application Server for z/OS Liberty profile
             from CICS 
* OLAUTIL -  JCL/source for Cobol CICS test application utility. Able to
             test Register, Invoke, Host Service, Send Response APIs from
             this panel and update the deamon group/server/node names as
             well as the service name to run with.  This utility can be
             used for testing calling in both directions. Code from here
             can be used as samples for using these APIs

Note the OLAUTIL sample does GET CONTAINER INTOCCSID(37)from
CCSID(819)- ISO8859-1. Before running this sample to demonstrate a
WebSphere Application Server for z/OS Liberty profile to CICS flow,
ensure you are running z/OS Unicode services and can support this
conversion. Otherwise an AEZW CICS abend may occur. 

In addition to these samples, the sample application
OLASampleLiberty.ear is provided, which is installed into a Liberty
profile server and communicates with the samples.

## Installation

These instructions assume that the optimized local adapters function
has been configured using the instructions provided in the Knowledge
Center for WebSphere Application Server for z/OS Liberty profile.  The
user running the samples must have SAF authority to use the optimized
local adapters on the target Liberty profile server instance.

OLASampleLiberty.ear should be installed into a Liberty profile server
by modifying server.xml with the <application/> tag, or by placing it
in the dropins directory.

The jsp-2.2, servlet-3.0 and zosLocalAdapters-1.0 features should be
included in your server.xml.  An optimized local adapters connection
factory should be configured with the jndi name "eis/ola".  The
zosLocalAdapters configuration requires a three part name which
describes the Liberty profile server instance.  Some samples will
need to be edited to reference this name.

An example server.xml is shown below:

```
<server description="new server">
    <featureManager>
        <feature>jsp-2.2</feature>
        <feature>servlet-3.0</feature>
        <feature>zosLocalAdapters-1.0</feature>
    </featureManager>

    <httpEndpoint id="defaultHttpEndpoint"
                  host="*"
                  httpPort="9080"
                  httpsPort="9443" />

    <zosLocalAdapters wolaGroup="LIBERTY" wolaName2="LIBERTY" 
                      wolaName3="LIBERTY"/>

    <connectionFactory jndiName="eis/ola">
      <properties.ola RegisterName="OLASERVER"/>
    </connectionFactory>

    <application id="WolaSample" name="WolaSample" 
                 location="OLASampleLiberty.ear"/>
</server>

```

The rest of the sample files should be copied to a partitioned data
set with a LRECL of 80.  Each sample containing JCL should be modified
as appropriate for the target system.  For example, the dataset names
referenced in the JCL will need to change to match the dataset naming
convention on the system where they will be run.

You should submit the CSDUPDAT sample job to add the sample program
definitions to the CICS configuration.  Alternatively, you could add
the definitions manually using the CEDA transaction, using the
CSDUPDAT sample job as a reference.  Once the CICS configuration has
been updated, the sample programs will be available in the CICS
region.

## OLAUTIL

The OLAUTIL sample can be used to verify that the optimized local
adapters are configured correctly for inbound and outbound
communication to a CICS region.

1. Edit and submit the OLAMAP job to build the CICS map set module.
This is used by the OLAUTIL sample.  You need to change the MAPLIB and
DSCTLIB parameters to your own dataset names.
2. Edit and submit the OLAUTIL job.  The OLAUTIL job uses the output
of the OLAMAP job.
3. Start your CICS region.  Verify that the definitions for OLAMAP and
OLAUTIL are available, either by running the CSDUPDAT sample job, or
by manually entering the definitions using the CEDA transaction.
4. Start the optimized local adapters task related user exit by
running the BBOC transaction, passing "START_TRUE" as a parameter.
This enabled the optimized local adapters function in the CICS region.
5. Run the OLAU transaction.  This displays the optimized local
adapters utility panel.

To verify communication from CICS to a Liberty profile server:

1. Update the 'Wola group', 'Wola name 2' and 'Wola name 3' fields to
match what was specified in the zosLocalAdapters configuration in
server.xml.  In the 'Send message data' field, enter some text.
2. Use 'PF4' to send the message to the Liberty profile server.  The
utility will call the EchoBean sample application, which returns the
'Send message data' field text back as a response, which is displayed
in the 'Received mesage data' field.  If an error occurred, it will be
displayed at the bottom of the utility panel.

To verify communication from a Libery profile server to CICS:

1. Update the 'Wola group', 'Wola name 2' and 'Wola name 3' fields to
match what was specified in the zosLocalAdapters configuration in
server.xml.  Change 'Service name' to a short string such as
'MYSERVICE'.
2. Use 'PF5' to host the service name.  The utility program is now
waiting to receive a request from the Liberty profile server instance.
3. On your local workstation, open a web browser to the optimized
local adapters sample application installed on the Liberty profile
server instance.  The url is described below:
http://my.host.name:port/OLASampleLibertyWeb/
4. In the form which is displayed in the web browser, change 'OLA
Register Name' to CICSTEST, and 'OLA Service Name' to match the
service name you entered in step 1.  Click the "Run WAS->External
address space test" button.
5. The utility program has now received a request.  The request data
is displayed in the 'Received message data' field.  Enter some text in
the 'Send message data' field and use 'PF6' to send the response back
to the Liberty profile server instance.
6. The response will be displayed in the web browser.

## OLACB01

This sample demonstrates how to use the CICS link server to link to a
CICS program using a COMMAREA.  To compile the sample:

1. Edit and submit the OLACB01 sample JCL.  Ensure that the output
dataset is contained in your CICS DFHRPL concatenation.

This sample requires the CICS link server.  To start the link server:

1. If you have not already done so, start the optimized local adapters
task related user exit by running the BBOC transaction, passing
"START_TRUE" as a parameter.  This enabled the optimized local
adapters function in the CICS region. 
2. Start a CICS link server by running the BBOC transaction, passing
the START_SRVR, RGN, DGN, NDN, SVN and SVC parameters.  The DGN
parameter should be set to the wolaGroup name in server.xml, the NDN
parameter should be set to the wolaName2 name in server.xml, and the
SVN parameter should be set to the wolaName3 name in server.xml.  The
RGN parameter represents the registration name which will identify
this link server.  The SVC parameter should be set to '*' indicating
this link server can service any service name.  An example BBOC
invocation is below: 
BBOC START_SRVR RGN=OLASERVER DGN=LIBERTY NDN=LIBERTY SVN=LIBERTY SVC=*

To call the sample:
1. On your local workstation, open a web browser to the optimized
local adapters sample application installed on the Liberty profile
server instance.  The url is described below:
http://my.host.name:port/OLASampleLibertyWeb/
2. In the form which is displayed in the web browser, change 'OLA
Register Name' to the value of the RGN parameter, and change 'OLA
Service Name' to OLACB01.
3. Click the "Run WAS->External address space test" button.  Program
OLACB01 will run and the response will be returned to the browser.

## OLACB02

This sample demonstrates how to use the CICS link server to link to a
CICS program using a single container.  To compile the sample:

1. Edit and submit the OLACB02 sample JCL.  Ensure that the output
dataset is contained in your CICS DFHRPL concatenation.

This sample requires the CICS link server.  If you have not already
done so, follow the directions for the OLACB01 sample to start the
link server.

To call the sample:
1. On your local workstation, open a web browser to the optimized
local adapters sample application installed on the Liberty profile
server instance.  The url is described below:
http://my.host.name:port/OLASampleLibertyWeb/
2. In the form which is displayed in the web browser, change 'OLA
Register Name' to the value of the RGN parameter, and change 'OLA
Service Name' to OLACB02.  Check the box labeled 'UseContainers' to
indicate the data should be placed in a container.  The default values
for 'CICS-Link Request Container Id' and 'CICS-Link Response Container
Id' should be used.
3. Click the "Run WAS->External address space test" button.  Program
OLACB02 will run and the response will be returned to the browser.
4. In addition to the default response container name, OLACB02 also
returns container names CURRENTDATE and CURRENTTIME.  You can change
the 'CICS-Link Response Container Id' to one of these strings to see
the output from that container.

## OLACB03 - OLACB04

These samples receive requests from a Liberty profile server instance
using the WOLA APIs instead of the CICS link server.  To compile the
samples, edit and submit the OLACB03 and OLACB04 sample JCL.  Ensure
that the output dataset is contained your CICS DFHRPL.

To run the samples in a Batch environment, the OLABATCH sample job can
be used.  In a CICS environment, a transaction definition should be
created to run the OLACB03 or OLACB04 program.

The sample application used to test OLACB01 and OLACB02 can be used to
drive OLACB03 and OLACB04.  Be sure to edit the register name and
service name fields to match the register name and service name
defined in the OLACB03 and OLACB04 sample code.

## OLACB05 - OLACB06

These samples send requests to a Liberty profile server instance using
the WOLA APIs.  To compile the samples, edit and submit the OLACB05
and OLACB06 sample JCL.  Ensure that the output dataset is contained
in your CICS DFHRPL.

To run the samples in a Batch environment, the OLABATCH sample job can
be used.  In a CICS environment, a transaction definition should be
created to run the OLACB05 or OLACB06 program.

Both of these samples invoke the 'EchoBean' contained in the optimized
local adapters sample application installed in your Liberty profile
server instance.

## OLACB10

This sample demonstrates how to use the CICS link server to link to a
CICS program using multiple containers.  To compile the sample:

1. Edit and submit the OLACB10 sample JCL.  Ensure that the output
dataset is contained in your CICS DFHRPL concatenation.

This sample requires the CICS link server.  If you have not already
done so, follow the directions for the OLACB01 sample to start the
link server.

To call the sample:
1. On your local workstation, open a web browser to the optimized
local adapters sample application installed on the Liberty profile
server instance.  The url is described below:
http://my.host.name:port/OLASampleLibertyWeb/
2. In the form which is displayed in the web browser, change 'OLA
Register Name' to the value of the RGN parameter, and change 'OLA
Service Name' to OLACB10.  Check the box labeled 'UseChannel' to
indicate the data should be placed in multiple containers.  The
default value for 'CICS-Link Channel Id' should be used.
3. Click the "Run WAS->External address space test" button.  Program
OLACB10 will run and the response will be returned to the browser.

## OLACB11 - OLACB12

These samples demonstrate how to use the CICS link server to link to a
CICS program using multiple containers.  Both samples perform some
modification to the containers.  The instructions to run these samples
are the same as for OLACB10.

## OLACC01

This sample sends a request to a Liberty profile server instance using
the WOLA APIs.  To compile the sample, edit and submit the OLACC01
sample JCL.  The sample was designed to be run as a batch job, but
can also be run from CICS.

To run the sample in a Batch environment, the OLABATCH sample job can
be used.  The wolaGroup, wolaName2 and wolaName3 can be specified in
the OLABATCH sample job by adding the parameter PARM='/GROUP NAME2
NAME3', to the JCL, where GROUP is wolaGroup, NAME2 is wolaName2 and
NAME3 is wolaName3 from server.xml.

This sample invokes the 'EchoBean' contained in the optimized
local adapters sample application installed in your Liberty profile
server instance.

## OLACC02

This sample sends a request to a Liberty profile server instance using
the WOLA APIs.  The request then issues a separate request back to the
OLACC02 sample.  To compile the sample, edit and submit the OLACC02
sample JCL.  The sample was designed to be run as a batch job, but
can also be run from CICS.

To run the sample in a Batch environment, the OLABATCH sample job can
be used.  The wolaGroup, wolaName2 and wolaName3 can be specified in
the OLABATCH sample job by adding the parameter PARM='/GROUP NAME2
NAME3', to the JCL, where GROUP is wolaGroup, NAME2 is wolaName2 and
NAME3 is wolaName3 from server.xml.

This sample invokes the 'RoundTripBean' contained in the optimized
local adapters sample application installed in your Liberty profile
server instance.

## OLACC10

This sample is similar to OLACB10.  OLACC10 is written in the C
programming language, while OLACB10 is written in COBOL.  The
instructions for compiling and running OLACC10 are the same as for
OLACB10, substituting OLACC10 for OLACB10.

## BBOACPLT, BBOACPL2, BBOACPL3

These samples are meant to be used as PLTPI transactions in a CICS
region.  They can perform WOLA initialization automatically when the
CICS region starts.  

The DFHPLTOL sample JCL can be used to compile these programs.  Refer
to the CICS Transaction Server documentation for directions to install
the compiled PLTIP transactions in the CICS region.
