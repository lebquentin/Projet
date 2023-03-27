```r

while :
do
curl -s https://investir.lesechos.fr/cours/actions/engie-engi-fr0010208488-xpar | html2text | grep -Eo 'Cours[0-9,]+' | cut -c6- >> data.csv
echo 'Donnees ecrites'
 
sleep 60

done

```
