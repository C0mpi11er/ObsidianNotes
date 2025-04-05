### Censys Cheat Sheet

**Censys** is a search engine that allows users to search for and analyze data related to internet-connected devices and services. It provides detailed information about the security posture of these devices, making it a valuable tool for penetration testers, security researchers, and network administrators.

### Key Features

**1. Basic Search:**
   - **URL:** `https://search.censys.io`
   - **Search by IP Address:** Enter the IP address in the search bar.
   - **Search by Domain:** Enter the domain name in the search bar.
   - **Search by Certificate:** Enter the certificate details in the search bar.

**2. Advanced Search:**
   - **Filters:** Use filters to narrow down search results based on various criteria such as country, service, protocol, and more.
   - **Query Language:** Use Censys Query Language (CQL) to create complex queries.

**3. Certificate Search:**
   - **Certificate Details:** Search for certificates by common name, issuer, or other certificate details.
   - **Certificate Transparency:** Monitor certificate transparency logs for new certificates issued to your domain.

**4. Host Search:**
   - **Host Details:** Search for hosts by IP address, domain, or other host-related criteria.
   - **Service Information:** View detailed information about services running on the host, including open ports, protocols, and banners.

**5. Report Generation:**
   - **Custom Reports:** Generate custom reports based on your search results.
   - **Export Data:** Export search results to CSV, JSON, or other formats for further analysis.

**6. API Access:**
   - **API Key:** Obtain an API key from your Censys account settings.
   - **API Endpoints:**
     - **Hosts:** `https://search.censys.io/api/v2/hosts`
     - **Certificates:** `https://search.censys.io/api/v2/certificates`
     - **Websites:** `https://search.censys.io/api/v2/websites`
   - **API Documentation:** Refer to the [Censys API documentation](https://search.censys.io/api) for detailed information on endpoints and parameters.

### Use Cases

**1. Vulnerability Assessment:**
   - **Identify Vulnerable Services:** Use Censys to identify services running on your network that are known to be vulnerable.
   - **Patch Management:** Prioritize patching efforts based on the exposure of vulnerable services.

**2. Threat Intelligence:**
   - **Monitor for Malicious Activity:** Use Censys to monitor for malicious activity related to your organization's IP addresses or domains.
   - **Incident Response:** Quickly gather information about compromised hosts or services during an incident response.

**3. Compliance and Audit:**
   - **Compliance Checks:** Ensure that your organization's internet-facing assets comply with security policies and regulations.
   - **Audit Trails:** Maintain audit trails of your organization's internet-facing assets for compliance and audit purposes.

**4. Network Discovery:**
   - **Asset Inventory:** Create an inventory of your organization's internet-facing assets.
   - **Network Mapping:** Map out your organization's network topology based on Censys data.

**5. Red Teaming:**
   - **Reconnaissance:** Use Censys to gather information about your target's internet-facing assets during a red team engagement.
   - **Exploitation:** Identify potential attack vectors based on the services and configurations exposed by the target.

### Example API Request (Python)

Here's an example of how to use the Censys API to search for hosts:

```python
import requests

api_key = 'YOUR_API_KEY'
query = 'services.service_name: "http"'

response = requests.post(
    'https://search.censys.io/api/v2/hosts/search',
    headers={'Content-Type': 'application/json', 'Authorization': f'Bearer {api_key}'},
    json={'query': query}
)

print(response.json())
```

Sure, let's break down the Python code for using the Censys API to search for hosts.

### Code Breakdown

```python
import requests
```

- **Importing the `requests` library:** This library is used to make HTTP requests in Python. It allows you to send HTTP/1.1 requests easily.

```python
api_key = 'YOUR_API_KEY'
query = 'services.service_name: "http"'
```

- **API Key:** Replace `'YOUR_API_KEY'` with your actual Censys API key. This key is required to authenticate your requests to the Censys API.
- **Query:** The `query` variable contains the search query in Censys Query Language (CQL). In this example, the query searches for hosts that have the HTTP service running.

```python
response = requests.post(
    'https://search.censys.io/api/v2/hosts/search',
    headers={'Content-Type': 'application/json', 'Authorization': f'Bearer {api_key}'},
    json={'query': query}
)
```

- **Making the POST Request:** The `requests.post` function sends a POST request to the Censys API endpoint `https://search.censys.io/api/v2/hosts/search`. The `headers` parameter includes the content type and the authorization token for authentication. The `json` parameter sends the search query in the request body.
  - **URL:** The endpoint URL for searching hosts.
  - **Headers:** The `Content-Type` header specifies that the request body is in JSON format. The `Authorization` header includes the API key for authentication.
  - **JSON Payload:** The `json` parameter sends the search query in the request body.

```python
print(response.json())
```

- **Printing the Response:** The `response.json()` method parses the JSON response from the API and prints it. This response contains the search results from Censys, including details about the hosts that match the query.

### Example Response

The response from the Censys API will be a JSON object containing various details about the hosts that match the query, including:

- **IP Address:** The IP address of the host.
- **Services:** Information about the services running on the host, including the service name, port, and protocol.
- **Certificates:** Details about the SSL/TLS certificates associated with the host.
- **Geolocation:** Geolocation information about the host, such as the country, city, and latitude/longitude.

### Example JSON Response

```json
{
    "status": "ok",
    "results": [
        {
            "ip": "8.8.8.8",
            "services": [
                {
                    "service_name": "http",
                    "port": 80,
                    "protocol": "tcp",
                    "extracted_certificates": [
                        {
                            "parsed": {
                                "subject": {
                                    "common_name": "example.com"
                                },
                                "issuer": {
                                    "common_name": "Example CA"
                                },
                                "validity": {
                                    "not_before": "2023-01-01T00:00:00Z",
                                    "not_after": "2024-01-01T00:00:00Z"
                                }
                            }
                        }
                    ]
                }
            ],
            "location": {
                "country": "US",
                "city": "Mountain View",
                "latitude": 37.422,
                "longitude": -122.084
            }
        }
    ]
}
```

This JSON response provides a comprehensive overview of the hosts that match the search query, including details about the services running on the hosts, associated certificates, and geolocation information.