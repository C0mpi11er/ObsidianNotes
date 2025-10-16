
->> sometimes the logged in cookie can be the username and password ensure to check it thoroughly


->> some cases used password maybe hashed in the cookie



# note
injecting this headers pluse attacker server gives you man in middle attack (portfowarding)
- `X-Forwarded-Host: attacker.com`
    
- `X-Original-Host: attacker.com`
    
- `X-Host: attacker.com`
    
- `Forwarded: host=attacker.com;proto=http`
    
- `X-Forwarded-Proto: http`
    
- `X-Forwarded-Port: 80`
    

📌 If any of these are reflected in:

- Emails (password reset links)
    
- Location redirects
    
- Absolute URLs in responses  
    → That’s a valid **injection point**.