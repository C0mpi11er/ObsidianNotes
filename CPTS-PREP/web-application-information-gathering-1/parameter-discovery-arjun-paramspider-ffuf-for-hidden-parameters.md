# 🛠️ Parameter Discovery - arjun, paramspider, ffuf for Hidden Parameters

> [!ABSTRACT] This note outlines the process of discovering hidden parameters in web applications using the tools `arjun`, `paramspider`, and `ffuf`. It provides detailed steps to identify potential injection points or hidden inputs that can be exploited.

---

## 📜 Overview

### Arjun

`Arjun` is a tool used for detecting SQL injection, LDAP injection, etc., by analyzing responses. It works well with curl and supports several input types.

> [!INFO] 
> **Installation**: Use pip to install `arjun`.
>
> ```bash
> pip3 install arjun
> ```

### Paramspider

`ParamSpider` is an API-driven tool designed for enumerating potential endpoints from a website using the Bing and Baidu APIs. It can help in finding hidden parameters.

> [!INFO]
> **Installation**: Use pip to install `paramspider`.
>
> ```bash
> pip3 install paramspider
> ```

### FFuf

`FFuf` is a fast URL Fuzzer that supports HTTP Basic/Digest Authentication, custom data sources, and many more features. It's highly effective for finding hidden parameters in web applications.

> [!INFO]
> **Installation**: Use `go get` to install `ffuf`.
>
> ```bash
> go get -u github.com/ffuf/ffuf
> ```

---

## 🚀 Steps & Workflow

### Using Arjun

1. **Scan with Arjun**:
    > [!CHECK]
    > Start scanning the target URL.
    
    ```bash
    arjun --url http://example.com
    ```
   
2. **Analyze Responses**: 
    - Look for unexpected responses that indicate potential injection points or hidden inputs.

### Using Paramspider

1. **Enumerate Endpoints**:
    > [!CHECK]
    > Run `paramspider` to find all possible endpoints including hidden parameters.
    
    ```bash
    paramspider --url http://example.com
    ```

2. **Review Output**: 
    - Analyze the output for any hidden parameters or unusual URLs.

### Using FFuf

1. **Fuzz with FFuf**:
    > [!CHECK]
    > Use `ffuf` to fuzz the target URL for hidden parameters.
    
    ```bash
    ffuf -u http://example.com/endpoint FUZZ
    ```

2. **Custom Data Sources**: 
    - Utilize custom data sources or predefined word lists if available.

---

## 📝 Results & Observations

### Arjun Output Analysis

- Analyze the output from `arjun` for potential SQL injection points and other vulnerabilities.
  > [!SUCCESS]
  > If any suspicious inputs are detected, proceed to further testing with `sqlmap`.

### Paramspider Output Review

- Go through the endpoints identified by `paramspider`.
  > [!SUCCESS] 
  > Hidden parameters or unusual URLs can be potential targets for exploitation.

### FFuf Fuzzing Results

- Examine the results from `ffuf` fuzzing.
  > [!SUCCESS]
  > If any hidden parameters are found, they may indicate vulnerabilities such as SQL injection, XSS, etc.

---

## 🔒 Security Considerations & Hazards

> [!WARNING] 
> **Do not use these tools on production servers without permission**. Misuse can lead to legal consequences and ethical violations.

### Potential Risks

- Running these tools on unapproved systems could trigger security alarms or even cause service disruptions.
- Ensure you have proper authorization before testing any target system with the above methods.

---

## 🧭 Next Steps & Follow-ups

1. **Further Testing**:
   > [!TODO]
   - If hidden parameters are found, perform further tests to confirm their impact and exploitability.

2. **Reporting Findings**:
   - Document all findings in a report for stakeholders or as part of OSCP prep materials.
   
---

## 🧑‍💻 Technical Details

### Common Commands & Options

- Arjun
  ```bash
  arjun --url http://example.com --verbose
  ```
  
- Paramspider
  ```bash
  paramspider --url http://example.com --threads 50 --depth 2
  ```

- FFuf
  ```bash
  ffuf -u http://example.com/endpoint?FUZZ=param -w /path/to/wordlist.txt
  ```
  
---

## 📜 Summary

> [!SUCCESS] 
> By utilizing `arjun`, `paramspider`, and `ffuf` effectively, you can uncover hidden parameters in web applications that might lead to critical vulnerabilities. Ensure thorough testing with caution and proper authorization.

---

# ⚙️ Conclusion & Next Actions

- **Continue Testing**: Follow up on findings by conducting further tests.
  > [!TODO]
  - Document all steps taken and results obtained for future reference or OSCP preparation.

> [!NOTE] 
Ensure that you have the necessary permissions before deploying these tools in a production environment.