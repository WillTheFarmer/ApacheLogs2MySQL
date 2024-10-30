this release imports 2 LogFormats - default combined and customized extended
LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
# h - Remote hostname. Will log the IP address if HostnameLookups is set to Off, which is the default. If it logs the hostname for only a few hosts, you probably have access control directives mentioning them by name. See the Require host documentation.
# l - Remote logname (from identd, if supplied). This will return a dash unless mod_ident is present and IdentityCheck is set On. Not importing or using due to required Apache Module mod_ident causing serious latency problems. By default value is '-'. 
# u - Remote user if the request was authenticated. May be bogus if return status (%s) is 401 (unauthorized).
# t - The time, in the form given by format, which should be in an extended strftime(3) format (potentially localized). If the format starts with begin: (default) the time is taken at the beginning of the request processing. 
# r - First line of request. This variable contains three data points: req_method, req_uri and req_protocol.
# s - Status. For requests that have been internally redirected, this is the status of the original request. Use %>s for the final status.
# O - Bytes sent, including headers. May be zero in rare cases such as when a request is aborted before a response is sent. You need to enable mod_logio to use this.
# Referer - The referrer Apache log refers to the URL of the webpage that directed a user to the requested resource. It is logged in the access log and helps track user navigation and traffic sources.
# User-Agent - This is the value of User-Agent, the browser identification string. 

# LogFormat "\"%h\",%t,%I,%O,%S,%B,%{ms}T,%D,%^FB,%>s,\"%H\",\"%m\",\"%U\",\"%{Referer}i\",\"%{User-Agent}i\",\"%{farmwork.app}C\",\"%{Host}i\"" extended
# not needed - %A,%l,%f,%u,%b,%q,%p,\"%{Content-Type}o\",
# %h - Remote hostname. Will log the IP address if HostnameLookups is set to Off, which is the default.
# %t - Time the request was received, in the format [18/Sep/2011:19:18:28 -0400]. The last number indicates the timezone offset from GMT
# %I - Bytes received, including request and headers.
# The %O format provided by mod_logio will log the actual number of bytes sent over the network.
# %O - Bytes sent, including headers.
# %S - Bytes transferred (received and sent), including request and headers, cannot be zero. This is the combination of %I and %O. 
# %{ms}T - The time taken to serve the request, in milliseconds.
# %D - The time taken to serve the request, in microseconds.
# %^FB - Delay in microseconds between when the request arrived and the first byte of the response headers are written. 
# The %B format strings do not represent the number of bytes sent to the client, but simply the size in bytes of the HTTP response (which will differ, for instance, if the connection is aborted, or if SSL is used).
# %B - Size of response in bytes, excluding HTTP headers.
# %s - Status. For requests that have been internally redirected, this is the status of the original request.
# %H - The request protocol.
# %m - The request method.
# %U - The URL path requested, not including any query string.
# "%{Referer}i - Referer log format
# %{User-agent}i - Agent (Browser) log format
# %{VARNAME}C - The contents of cookie VARNAME in the request sent to the server. Only version 0 cookies are fully supported.
# %{VARNAME}i - The contents of VARNAME: header line(s) in the request sent to the server.

# this release imports only default ErrorLogFormat - "[%{u}t] [%-m:%l] [pid %P:tid %T] %7F: %E: [client\ %a] %M% ,\ referer\ %{Referer}i"
https://documentation.help/httpd-2.4/core.html#errorlog
For example, here's what would happen if you added modifiers to the %{Referer}i token, which logs the Referer request header.
Modified Token	Meaning
%-{Referer}i	Logs a - if Referer is not set.
%+{Referer}i	Omits the entire line if Referer is not set.
%4{Referer}i	Logs the Referer only if the log message severity is higher than 4.
Some format string items accept additional parameters in braces.
Format String	Description
%%	The percent sign
%a	Client IP address and port of the request
%{c}a	Underlying peer IP address and port of the connection (see the mod_remoteip module)
%A	Local IP-address and port
%{name}e	Request environment variable name
%E	APR/OS error status code and string
%F	Source file name and line number of the log call
%{name}i	Request header name
%k	Number of keep-alive requests on this connection
%l	Loglevel of the message
%L	Log ID of the request
%{c}L	Log ID of the connection
%{C}L	Log ID of the connection if used in connection scope, empty otherwise
%m	Name of the module logging the message
%M	The actual log message
%{name}n	Request note name
%P	Process ID of current process
%T	Thread ID of current thread
%{g}T	System unique thread ID of current thread (the same ID as displayed by e.g. top; currently Linux only)
%t	The current time
%{u}t	The current time including micro-seconds
%{cu}t	The current time in compact ISO 8601 format, including micro-seconds
%v	The canonical ServerName of the current server.
%V	The server name of the server serving the request according to the UseCanonicalName setting.
\  (backslash space)	Non-field delimiting space
%  (percent space)	Field DELIMITER (no output)
The log ID format %L produces a unique id for a connection or request. This can be used to correlate which log lines belong to the same connection or request, which request happens on which connection. A %L format string is also available in mod_log_config to allow to correlate access log entries with error log lines. If mod_unique_id is loaded, its unique id will be used as log ID for requests.

#Example (default format for threaded MPMs)
ErrorLogFormat "[%{u}t] [%-m:%l] [pid %P:tid %T] %7F: %E: [client\ %a] %M% ,\ referer\ %{Referer}i"
This would result in error messages such as:
[Thu May 12 08:28:57.652118 2011] [core:error] [pid 8777:tid 4326490112] [client ::1:58619] File does not exist: /usr/local/apache2/htdocs/favicon.ico

Notice that, as discussed above, some fields are omitted entirely because they are not defined.
#Example (similar to the 2.2.x format)
ErrorLogFormat "[%t] [%l] %7F: %E: [client\ %a] %M% ,\ referer\ %{Referer}i"
#Advanced example with request/connection log IDs
ErrorLogFormat "[%{uc}t] [%-m:%-l] [R:%L] [C:%{C}L] %7F: %E: %M"
ErrorLogFormat request "[%{uc}t] [R:%L] Request %k on C:%{c}L pid:%P tid:%T"
ErrorLogFormat request "[%{uc}t] [R:%L] UA:'%+{User-Agent}i'"
ErrorLogFormat request "[%{uc}t] [R:%L] Referer:'%+{Referer}i'"
ErrorLogFormat connection "[%{uc}t] [C:%{c}L] local\ %a remote\ %A"