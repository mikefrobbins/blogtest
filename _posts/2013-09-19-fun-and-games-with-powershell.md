---
ID: 8254
post_title: Fun and Games with PowerShell
author: Mike F Robbins
post_excerpt: ""
layout: post
permalink: >
  https://mikefrobbins.com/2013/09/19/fun-and-games-with-powershell/
published: true
post_date: 2013-09-19 07:30:30
---
My daughter has been learning Python which I know nothing about (I'm not a developer). I took a look at one of the programs that she's been working on and said I don't know Python, but I can create something better than that in PowerShell.

I hadn't previously used a "do until" loop in PowerShell so I decided this was as good as an excuse as any to use one since the loop has to run at least once anyway and it gave me a chance to learn something new.
<pre class="wrap:true lang:ps decode:true">Add-Type -AssemblyName System.Speech

[int]$guess = 0
[int]$attempt = 0
[int]$number = Get-Random -Minimum 1 -Maximum 100

$voice = New-Object System.Speech.Synthesis.SpeechSynthesizer
$voice.Speak("Ahoy matey! I'm the Dreaded Pirate Robbins, and I have a secret!
              It's a number between 1 and 100. I'll give you 7 tries to guess it.")

do {
    $voice.SpeakAsync("What's your guess?") | Out-Null

    try {
        $guess = Read-Host "What's your guess?"

        if ($guess -lt 1 -or $guess -gt 100) {
            throw
        }
    }
    catch {
        $voice.Speak("Invalid number")
        continue
    }

    if ($guess -lt $number) {
        $voice.Speak("Too low, yee scurry dog!")
    }
    elseif ($guess -gt $number) {
        $voice.Speak("Too high, yee land lubber!")
    }

    $attempt += 1
}
until ($guess -eq $number -or $attempt -eq 7)

if ($guess -eq $number) {
    $voice.Speak("Avast! Yee guessed my secret number, yee did!")
}
else {
    $voice.Speak("Yee out of guesses! Better luck next time, yee matey!
                  My secret number was $number")
}</pre>
I would like to thank <a href="http://twitter.com/theJasonHelmick" target="_blank">Jason Helmick</a> for the tip on using the .NET Framework instead of COM for the speech as well as <a href="http://rohnspowershellblog.wordpress.com/" target="_blank">Rohn Edwards</a> who originally wrote a version of the code shown below that used COM which I translated into using the .NET Framework as well:
<pre class="wrap:true lang:ps decode:true">#Requires -Version 3.0
Add-Type -AssemblyName System.Speech

$voice = New-Object System.Speech.Synthesis.SpeechSynthesizer

$voice.GetInstalledVoices() | ForEach-Object {
    if ($_.VoiceInfo.Id -match "TTS_MS_(?&lt;culture&gt;\w{2}-\w{2})_(?&lt;name&gt;[^_]+)") {
        $Name = $matches.name
        $Culture = [cultureinfo]$matches.culture | select -expand DisplayName
    }
    else {
        Write-Warning "Couldn't get info from $($_.VoiceInfo.Id)"
        $Name = "something I'm not going to share with you"
        $Culture = "something you're not going to find out"
    }

    $voice.SelectVoice($_.voiceinfo.name)
    $voice.Speak("Hi, my name is $Name and my speaking style is $Culture")
}</pre>
µ