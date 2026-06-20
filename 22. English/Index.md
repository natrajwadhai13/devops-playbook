---
title: "• Index"
parent: "22. English"
nav_order: 1
has_children: true
---

folders=(
"01-English-Basics"
"02-Parts-Of-Speech"
"03-Articles"
"04-Singular-Plural"
"05-Countable-Uncountable"
"06-Pronouns"
"07-Tenses"
"08-Helping-Verbs"
"09-Modal-Verbs"
"10-Prepositions"
"11-Determiners"
"12-Question-Formation"
"13-Active-Passive"
"14-Direct-Indirect"
"15-Conditionals"
"16-Punctuation"
"17-Vocabulary"
"18-Common-Mistakes"
"19-Daily-Speaking"
"20-Office-English"
"21-Email-Writing"
"22-Meeting-English"
"23-Interview-English"
"24-DevOps-English"
"25-GitHub-Writing"
"26-Exercises"
"27-Speaking-Challenges"
"28-Office-Examples"
"29-Career-English"
"30-Revision"
)

for folder in "${folders[@]}"; do
mkdir -p "$folder"
echo "# ${folder#*-}" > "$folder/README.md"
done
