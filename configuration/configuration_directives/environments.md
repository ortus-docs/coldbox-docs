# Environments

The configuration CFC has embedded environment control and detection built-in. In this structure you will setup the application environments and their associated regular expressions for its `cgi` host names to match for you automatically. If the framework matches the regex with the associated `cgi.http_host` then it will set a setting called `Environment` in your configuration settings and will then go ahead and look for that environment setting name in your CFC as a method by convention. That's right, it will check if your CFC has a method with the same name as the environment and if it exists it will call it for you. Here is where you basically override, remove or add any settings according to your environment.

