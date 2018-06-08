# AD
Active Directory
# Translate SIDs

Code

 #xxx domain script


#Dsquery group -samid GROUPNAME | dsget group -members > c:\GROUPNAME.txt


#Copy file/text to  then run script below to translate sid's to names



　


#xx1 SID = sid_number1


#xx2 SID = sid_number2



cls



$filelist = Get-Item -Path C:\Users\username\Documents\domain\*



$file = $filelist[54].Name



$Sids = Get-Content -Path "C:\Users\username\Documents\domain\$file"


$Sids = $Sids | Where-Object {$_}



 $finalsids = $sids | ForEach-Object {$_ -replace '"CN=',"" `


                                         -replace ',CN=ForeignSecurityPrincipals,DC=domain,DC=domain"',""


 }  



$finalarray = @()


Write-Host $file -ForegroundColor Yellow -BackgroundColor Black


ForEach ($Sid In $finalsids)


 {


     Write-Host $sid -ForegroundColor Cyan


     $SAM = $null


     Try


     {   


     $SAM  = (New-Object System.Security.Principal.SecurityIdentifier($SID)).Translate([System.Security.Principal.NTAccount]).Value


     }


     Catch


     {


     $ErrorMessage = $_.Exception.Message


     if ($ErrorMessage -match "Some or all identity references could not be translated")


     {


     $SAM = "Not found"


     }


     else {$ErrorMessage}


     }


     $psobject = New-Object System.Object


     $psobject | Add-Member -type NoteProperty -name Name -value $SAM


     $psobject | Add-Member -type NoteProperty -name SID -value $sid


     $finalarray += $psobject


     $SAM


     Write-Host ""


 }



$finalarray | Export-Csv -Path "C:\Users\username\Documents\domain\Export\$file.csv" -NoTypeInformation 
