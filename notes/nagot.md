##Git-Anteckningar

git status ## Se vad som ändrats
git add . ## Stagea ändringarna
git commit -m "...." ## spara en snapshot lokalt
git push  ## skicka ändringarna till Github
git push --force-with-lease ## tvingar igenom push oavsett vad och with-lease är den säkra varianten kollar först att ingen annan har hunnit pusha något så man inte skriver över den
git commit --amend ## ska man vara försiktig med på delade repon eftersom det skriver om historik. På eget solo repo är detta OK
