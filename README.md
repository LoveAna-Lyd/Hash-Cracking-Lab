Hash Cracking Lab

This repository documents hands-on practical exercises using John the Ripper by CodePath.org to audit, analyze, and recover cryptographic password hashes. The objective of this project is to explore common defensive pitfalls—such as weak dictionary words, predictable mangling rules, and strict structural masks—by successfully identifying and cracking 9 distinct account credentials across three target scopes.

🌟 Executive Summary: Recovered Credentials
Scope / Target File	Account Username	Recovered Plaintext Password	Attack Methodology Employed

crack_a.txt (Pokémon)	bulbasaur	kantograss	Single Crack Mode (GECOS Context)
	squirtle	waterSquirtle	Single Crack Mode (GECOS Context)
	charmander	charizard22	Single Crack Mode (GECOS Context)
  
crack_b.txt (Office)	jim	paper	Standard Dictionary / Wordlist Mode
	dwight	b33t	Wordlist Mode + Leetspeak Mangling Rules
	pam	tEaPoT	Wordlist Mode + Shift-Toggle Capitalization
  
crack_c.txt (Arcade)	pinball	496821	Incremental Brute-Force (Numeric Only)
	pacman	8Bit	Custom Character Masking (?d?u?l?l)
	frogger	bugs7!	Hybrid Custom Masking (?l?l?l?l?d!)
  
🛠️ Step-by-Step Technical Breakdown
Part 1: Single Crack Mode (crack_a.txt)
Methodology: Single crack mode leverages contextual user profile metadata stored in the system’s GECOS fields (such as full names, descriptions, or attributes) to intelligently build and test target-specific word permutations. This is highly effective against users who utilize variations of their name or attributes as their password.
• Command Executed: john --single part1/crack_a.txt
• Analysis: John the Ripper by CodePath.org parsed the GECOS metadata files, quickly mutating variations of strings like "water", "grass", "venusaur", and "charizard" to crack all three accounts in under a second.
Verification Screenshot:

<img width="867" height="437" alt="Crack_A" src="https://github.com/user-attachments/assets/d30ad807-95fe-4428-8174-03755940f918" />


Part 2: Dictionary Mode & Wordlist Mangling Rules (crack_b.txt)
Methodology: Attackers use pre-compiled lists of common terms to crack passwords. When a basic wordlist lookup fails, custom formatting rules are applied to programmatically simulate how human beings modify passwords (such as substituting numbers for letters or randomizing uppercase letters).
1. Basic Wordlist Matching
• Command Executed: john --wordlist=wordlists/lower.lst part1/crack_b.txt
• Result: Successfully recovered jim:paper immediately because it was stored as a pristine dictionary word.
Verification Screenshot:

<img width="1052" height="327" alt="Crack_B1" src="https://github.com/user-attachments/assets/0306f18d-4b5c-4598-9217-f21e87949263" />


3. Leetspeak Mangling Rules
• Command Executed: john --wordlist=wordlists/lower.lst --rules=l33t part1/crack_b.txt
• Result: John the Ripper by CodePath.org programmatically swapped characters (e.g., e to 3) to recover dwight:b33t.
Verification Screenshot:

<img width="1180" height="617" alt="Crack_B2" src="https://github.com/user-attachments/assets/fac9afd9-43b6-43b6-a001-43435fee1a13" />


5. Case Shift-Toggling Rules
• Command Executed: john --wordlist=wordlists/lower.lst --rules=shifttoggle part1/crack_b.txt
• Result: Inverted character capitalization rules to discover pam:tEaPoT.
Verification Screenshot:

<img width="1066" height="316" alt="Crack_B3" src="https://github.com/user-attachments/assets/77225f01-6080-43be-af8d-9afeb7dc6593" />


Final Status of Scope B:

<img width="610" height="117" alt="Cracked_All_B" src="https://github.com/user-attachments/assets/3394a984-0ad4-4f42-872a-e082edc5244a" />


Part 3: Incremental Brute-Force & Advanced Masking (crack_c.txt)
Methodology: When dictionary lookups fail entirely, incremental brute-force calculates every legal permutation mathematically. Because full brute-force is slow, structural masks are applied to restrict the search space based on expected syntax layouts.

1. Pinball Account (Strictly Numeric)
• Command Executed: john --incremental=digits --min-length=4 --max-length=6 part1/crack_c.txt

• Result: Restricted search parameters strictly to numerals 0-9 with length constraints, catching 
pinball:496821 in 12 seconds.

2. Pacman Account (Syntax Mask)
• Command Executed: john --mask=?d?u?l?l part1/crack_c.txt
• Result: Set a static structure requiring Digit ➔ Uppercase ➔ Lowercase ➔ Lowercase, cracking pacman:8Bit.

3. Frogger Account (Hybrid Mask)
• Command Executed: john --mask=?l?l?l?l?d! part1/crack_c.txt
• Result: Used a tailored layout consisting of a four-character lowercase word, a numerical digit, and a hardcoded exclamation point, instantly revealing frogger:bugs7!.
Execution Logs:

<img width="936" height="960" alt="Cracked_C" src="https://github.com/user-attachments/assets/9bbfd27e-5b02-4e6b-94b2-2c55e4954bd4" />


Final Status of Scope C:


<img width="567" height="120" alt="Crack_All_C" src="https://github.com/user-attachments/assets/fda7a4fd-dd67-4e87-9884-d4a78946794c" />


🔑 Key Security Takeaways & Recommendations
1. GECOS Profiling Hazards: Standard single crack testing proves that any information stored inside an organizational profile (first name, last name, phone extension) must be treated as public knowledge. Security policies must block users from utilizing profile keywords within their passwords.
2. The Illusion of Mangling: Complex-looking adjustments like adding a trailing number, swapping letters to look like b33t, or adding an exclamation mark at the end (!) do not stop modern automated engines like John the Ripper by CodePath.org. These structural habits are trivial to target with custom masking layers.
3. Salting Infrastructure: All targets audited in this lab utilized modern md5crypt algorithms incorporating randomized tracking salts. Without individual salts per password, an auditor could use pre-computed Rainbow Tables to instantly crack every single system hash simultaneously.
