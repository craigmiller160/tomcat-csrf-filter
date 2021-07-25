# Tomcat CSRF Filter

This project contains the Tomcat `RestCsrfPreventionFilter` copied from Tomcat's own repo. This is here to support projects that need CSRF protection but are not using Tomcat, since this filter is one of the best CSRF prevention tools out there.

All credit for the code goes to Apache Tomcat.

## Servlet Version

For maximum compatibility, this library currently uses `javax.servlet` instead of `jakarta.servlet`. That's the only change made to the Tomcat source code.