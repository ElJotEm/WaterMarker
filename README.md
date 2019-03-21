# WaterMarker
MacOS Automator Program using ffmpeg to render a Vimeo ready h264 mp4 with a custom watermark

Dependencies: 

- ffmpeg in the same directory as the exec
- Font in the same directory as the exec or change font path

Applescript:

```
on remove_extension(this_name)
	if this_name contains "." then
		set this_name to (the reverse of every character of this_name) as string
		set x to the offset of "." in this_name
		set this_name to (text (x + 1) thru -1 of this_name)
		set this_name to (the reverse of every character of this_name) as string
	end if
	return this_name
end remove_extension

on run {input, parameters}
	repeat with file_ in input
		tell application "System Events"
			set inputFilename to name of (get properties of file_)
		end tell
		set inputFilePath to quoted form of (POSIX path of file_)
		set outputFilePathTmp to quoted form of (my remove_extension(POSIX path of file_) & "_WM_ProRes444.mov")
		set outputFilePathFinal to quoted form of (my remove_extension(POSIX path of file_) & "_WM_H264.mp4")
		set watermarkText to the text returned of (display dialog "Gib einen Text für das Watermark ein" default answer "")
		tell application "Terminal"
			activate
			do script "ffmpeg -i " & inputFilePath & " -vf \"drawtext=text='" & watermarkText & "':x=w-tw-100:y=100:fontfile='Arial_Bold.ttf':fontsize=60:fontcolor=white@0x33\"  -vcodec prores_ks -pix_fmt yuva444p10le -profile:v 4444 -q:v 10 " & outputFilePathTmp & " && ffmpeg -i " & outputFilePathTmp & " -c:v libx264 -b:v 6M -maxrate 8M -bufsize 2M " & outputFilePathFinal & " && rm " & outputFilePathTmp
		end tell
	end repeat
	return input
end run
```
