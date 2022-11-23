To enable updates through Power Automate, please copy the following script into your Power Automate application : 

```
Display.SelectFolder Description: $'''Please select your Resource Pack Folder''' IsTopMost: True SelectedFolder=> SelectedFolder ButtonPressed=> ButtonPressed
LABEL Download
Web.DownloadFromWeb.DownloadToFolder Url: $'''https://github.com/ThatPaple/PapsPack/archive/refs/heads/main.zip''' DestinationFolder: SelectedFolder ConnectionTimeout: 30 FollowRedirection: True ClearCookies: True UserAgent: $'''Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.8.1.21) Gecko/20100312 Firefox/3.6''' Encoding: Web.Encoding.AutoDetect AcceptUntrustedCertificates: False DownloadedFile=> DownloadedFile
ON ERROR

END
IF Contains(ButtonPressed, $'''cancel''', True) THEN
    GOTO Finalise
END
IF (File.IfFile.Exists File: DownloadedFile) THEN
    Compression.UnzipFiles ArchivePath: DownloadedFile DestinationFolder: SelectedFolder
    File.Delete Files: DownloadedFile
END
IF (File.IfFile.DoesNotExist File: DownloadedFile) THEN
    Display.ShowMessageDialog.ShowMessage Title: $'''Error''' Message: $'''Could not dowload resource pack from GitHub!''' Icon: Display.Icon.ErrorIcon Buttons: Display.Buttons.RetryCancel DefaultButton: Display.DefaultButton.Button1 IsTopMost: False ButtonPressed=> BtnERR
    IF Contains(BtnERR, $'''retry''', True) THEN
        GOTO Download
    END
END
LABEL Finalise
Display.ShowMessageDialog.ShowMessageWithTimeout Title: $'''Goodbye!''' Message: $'''Thank you for using PapsPack Power Automate!''' Icon: Display.Icon.None Buttons: Display.Buttons.OK DefaultButton: Display.DefaultButton.Button1 IsTopMost: False Timeout: 5
```
