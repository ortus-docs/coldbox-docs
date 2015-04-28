# The Integration Test

Now that we have seen the handler code, let's see the testing code as well. One important thing to note is that I use URL variables in my tests instead of FORM variables. I use URL because if the tests are executed via the MXUnit Eclipse Plugin they execute via SOAP and ColdFusion does not translate the FORM structure in SOAP web services. If you only test via the browser then you are ok, but if you use the Eclipse Plugin you must use URL scope instead in your tests. The great thing about ColdBox is that it abstracts FORM and URL scope into the request collections!

