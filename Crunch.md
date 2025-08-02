
🛠️ Crunch – Wordlist Generator

Crunch is a powerful tool used to generate custom wordlists for password cracking and brute-force attacks. It allows fine-grained control over character sets, lengths, and patterns.
⚙️ Basic Usage

crunch <min> <max> [options]

    <min> / <max> – Minimum and maximum length of generated strings.

🔑 Common Options
Option	Description
-o <file>	Save output to a file.
-t <pattern>	Use a specific pattern (% = lowercase, @ = uppercase, ^ = number, , = symbol).
-s <string>	Start generating from a specific string (useful for resuming).
-e <string>	Stop generating at a specific string.
-f <charset.lst>	Use predefined charset files (e.g., mixalpha, numeric).
🧩 Example

crunch 3 3 -t %%% -o otp.txt -s 100 -e 200

    Generates 3-character strings matching pattern %%%.

    Starts at 100th string, ends at 200th.

    Saves output to otp.txt.

🧠 Use Cases

    Custom password list generation.

    Brute-force simulations.

    Fine-tuned attack wordlists based on patterns or policies.