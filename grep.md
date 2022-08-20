# Grep

## Search for pattern in all files

[Reference](https://stackoverflow.com/questions/16956810/how-do-i-find-all-files-containing-specific-text-on-linux).

```bash
egrep --exclude=all_errors.txt -rn "version|runtime|error" . > all_errors.txt
egrep "Binary file|CUDA error|GPU is lost" all_errors.txt > CUDA_error_GPU_is_lost.txt
```

Search for overruns and try to grab versions.

```bash
egrep --include=*.log -rn -m 5 "version|overrun|mismatch" . | egrep -B 1 "overrun|mismatch" > overruns.txt
```

Include or exclude certain files.

```bash
egrep --include=\*.{cpp,h} -rn "nvml" .
egrep --exclude=\*.o -rn "nvml" .
```

## Look for "overrun" in all syslogs

```bash
sudo egrep --include=syslog* -rn "overrun" /var/log
```

## Find matching file names

```bash
find . -name "*.json"
```

## Find largest files

```bash
find . -type f -exec du -a {} + | sort -n -r | head -n 20
```

## List number of lines in source code

This prunes directories named `build`, lists all files with the extensions `h`, `cpp` and `cu`, pipes them into word count and sorts numerically.

```bash
find . -type d -name build -prune -o -type f \( -name "*.h" -o -name "*.cpp" -o -name "*.cu" \) | xargs wc -l | sort -n
```

Additionally, you can use a set path instead of a directory name.

```bash
find . -path ./contrib/build -prune -o -type f \( -name "*.h" -o -name "*.cpp" -o -name "*.cu" \) | xargs wc -l | sort -n
```

## Search for THROW or THROW_IF spanning multiple lines

`-P` enables perl regex.  
`-z` spans multiple lines.  
`-o` outputs only matching strings.

First `sed` removes proceeding whitespaces.  
Second `sed` removes following whitespaces.  
`tr` deletes newlines.  
Last `sed` adds newlines at `src/`.

```bash
grep -r -Pzo "THROW(_IF)*\((\n|.)*?\);" src | \
sed -e 's/^[[:space:]]*//g' | \
sed -e 's/[[:space:]]*$//g' | \
tr --delete "\n" | \
sed -e "s/src/\nsrc/g" > tmp.txt
```
