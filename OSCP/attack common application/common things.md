

```
IP=10.129.42.195

printf "%s\t%s\n\n" "$IP" "app.inlanefreight.local dev.inlanefreight.local blog.inlanefreight.local" | sudo tee -a /etc/hosts
```


common ports and everything
```
nmap -p 80,443,8000,8080,8180,8888,1000 --open -oA web_discovery -iL scope_list
```

common domains
```
app.inlanefreight.local
dev.inlanefreight.local
drupal-dev.inlanefreight.local
drupal-qa.inlanefreight.local
drupal-acc.inlanefreight.local
drupal.inlanefreight.local
blog-dev.inlanefreight.local
blog.inlanefreight.local
app-dev.inlanefreight.local
jenkins-dev.inlanefreight.local
jenkins.inlanefreight.local
web01.inlanefreight.local
gitlab-dev.inlanefreight.local
gitlab.inlanefreight.local
support-dev.inlanefreight.local
support.inlanefreight.local
inlanefreight.local
10.129.201.50
```

## Eyewitness

EyeWitness can take the XML output from both Nmap and Nessus and create a report with screenshots of each web application present on the various ports using Selenium

```
eyewitness --web -x web_discovery.xml -d inlanefreight_eyewitness
```

it will screenshot  output

![[Pasted image 20240502204252.png]]


## Aquatone

Aquatone, as mentioned before, is similar to EyeWitness and can take screenshots when provided a .txt file of hosts or an Nmap .xml file with the -nmap flag