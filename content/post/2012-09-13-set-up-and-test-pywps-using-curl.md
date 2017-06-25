---
title: "Set up and test PyWPS using curl"
date: 2012-09-13 08:27:48+00:00
---

Install curl to test the service from terminal.

    
    <code>sudo aptitude install libcurl3
    </code>


# Here are some example requests (from terminal):

    
     curl "http://localhost/pywps/pywps.cgi?service=wps&version=1.0.0&request=getcapabilities"
     curl "http://localhost/pywps/pywps.cgi?service=wps&version=1.0.0&request=describeprocess
                                           &identifier=dummyprocess"
     curl "http://localhost/pywps/pywps.cgi?service=wps&version=1.0.0&request=execute
                                           &identifier=dummyprocess&datainputs=\[input1=10;input2=10\]"
