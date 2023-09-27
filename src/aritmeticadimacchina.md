L'Aritmetica di Precisione Finita
=================================

Lo studio matematico di un fenomeno naturale passa generalmente per una
prima fase di *modellizzazione*, seguita poi dalla risoluzione del
problema matematico. Spesso il problema matematico ha caratteristiche
tali per cui un approccio analitico non è appropriato e si preferisce
invece ricorrere a tecniche numeriche. In questo corso prendiamo in
considerazione alcuni problemi matematici fondamentali (soluzione di
sistemi lineari, soluzione di equazioni nonlineari, interpolazione,
calcolo di un integrale definito, risoluzione di un'equazione
differenziale) e studiamo degli algoritmi numerici che permettano di
risolvere tali problemi.

Numeri di macchina
==================

Richiamiamo innanzi tutto la nozione di numeri di macchina, ovvero come
i numeri sono rappresentati in un calcolatore. Distinguiamo due insiemi
numerici: i *numeri interi* e i *numeri floating-point* (che sono
approssimazioni dei numeri reali).

Numeri interi
-------------

Per i numeri interi, la rappresentazione di macchina con $s+1$ bit è del
tipo

```{math}
\underbrace{\alpha_0}_\textrm{segno}\,\underbrace{\alpha_1\,\alpha_2\,\dots\alpha_s}_{\textrm{cifre}}\,{\rm in}\,{\rm base}\, b
```

dove tipicamente si prende $b=2$. Abbiamo quindi un bit che rappresenta
il segno, seguito da $s$ bit che rappresentano le cifre binarie del
numero. Il numero $n$ ottenuto in questo modo è
```{math}
n=\left\{\begin{array}{cc}
\sum_{i=1}^s\alpha_i b^{s-i} & {\rm se}\,\,{\rm segno}\,\,+\\
\sum_{i=1}^s\alpha_i b^{s-i}-b^s & {\rm se}\,\,{\rm segno}\,\,-
\end{array}\right.
```

`````{admonition} Esempio
:class: tip
Sia $b=2$, $s=3$. Quali numeri interi posso rappresentare?

Se il segno è positivo, posso formare i numeri seguenti:

  |binario  |   001 |  010 |  011 |  100 |  101 |  110  | 111
  |----------| -----| -----| ----- |-----| -----| ----- |-----
  |decimale|     1   |  2  |   3   |  4  |   5  |   6   |  7

mentre $000$ corrisponde allo zero. Se il segno è negativo posso formare
i numeri interi da $-8$ a $-1$. Quindi l'insieme rappresentabile con
$b=2$ e $s=3$ è




$$


\{-8,-7,-6,-5,-4,-3,-2,-1,0, 1, 2, 3, 4, 5, 6, 7\}.


$$
`````

Numeri floating-point
=====================

Richiamiamo innanzi tutto il teorema di rappresentazione in base, che ci
servirà per definire i numeri floating-point. Noi siamo abituati a
scrivere i numeri reali con notazione posizionale in base 10, per
esempio


$$
123,45=1\times 10^2+2\times 10^1+3\times 10^0 + 4\times 10^{-1}+5\times 10^{-2}

$$

e spesso troviamo comodo usare una "notazione scientifica" del tipo


$$
123,45=0,12345\times 10^3.

$$
 Queste considerazioni si riassumono (e
generalizzano ad una base qualsiasi) come segue.

````{prf:theorem}
Sia $b\geq 2$
un numero intero fissato (*base*). Allora per ogni
$x\in\mathbb{R}\setminus\{0\}$ esistono

-   un numero intero $\eta$ *(esponente)*

-   numeri interi $\alpha_1,\alpha_2,\ldots$ *(cifre)* compresi fra $0$
    e $b-1$, con $\alpha_1\neq 0$, non definitivamente uguali a $b-1$,

tali che


$$
x={\rm segno}(x)\,\, b^{\eta}\,\sum_{i=1}^{\infty}\alpha_i b^{-i}.

$$
````

La rappresentazione data in questo teorema si dice *normalizzata*: la
condizione $\alpha_1\neq 0$ serve ad assicurare l'unicità della
rappresentazione. Allo stesso tempo, però, la normalizzazione impedisce
di rappresentare $x=0$.

Nella pratica ci interessano soprattutto i casi $b=10$ (la consueta base
decimale) e $b=2$ (poiché generalmente un computer usa campi di memoria
-- *bit* -- con due stati possibili).

Nel teorema di rappresentazione in base, l'esponente e il numero di
cifre variano in un insieme infinito. In un calcolatore, però, possiamo
allocare solo una quantità finita di memoria per la rappresentazione di
un numero, quindi dobbiamo imporre dei limiti su esponente e numero di
cifre.
````{prf:definition}
Siano fissati i seguenti numeri interi:

-   $b\geq 2$ (base),

-   $m,M\geq 1$ (limiti sull'esponente),

-   $t\geq 1$ (numero di cifre).

Si definisce l'insieme dei numeri di macchina floating-point


$$
\mathcal{F}_{b,t,m,M}=\left\{
x={\rm segno}(x)\,\, b^{\eta}\,\sum_{i=1}^{t}\alpha_i b^{-i}
\right\}\cup\{0\},

$$
 dove gli $\alpha_i$ sono interi tali che
$0\leq \alpha_i\leq b-1$ e $\alpha_1\neq 0$.
````

`````{admonition} Esempio
Osserviamo che $\mathcal{F}_{b,t,m,M}$ è un insieme finito, e che la
distribuzione dei suoi elementi sulla retta reale non è uniforme.

Costruiamo $\mathcal{F}_{b,t,m,M}$ per $b=2$, $t=3$, $M=m=1$.

Consideriamo dapprima il caso con segno positivo. Per $\eta=0$
otteniamo, in notazione binaria, i numeri


$$
(0,100)_2\qquad (0,101)_2\qquad (0,110)_2\qquad (0,111)_2,

$$
 cioè,
nell'ordine, $1/2$, $5/8$, $3/4$ e $7/8$. Per $\eta=1$ si ottiene $1$,
$5/4$, $3/2$ e $7/4$, e per $\eta=-1$ si ottiene $1/4$, $5/16$, $3/8$ e
$7/16$. Se il segno è negativo si ottengono gli opposti dei numeri
elencati. Graficamente l'insieme $\mathcal{F}_{2,3,1,1}$ si rappresenta
come
```{image} ./images/figurafloatingpoints.png
:width: 80%
:align: center
```
dove i numeri con esponente $-1$ sono indicati in blu, i numeri con
esponente $0$ sono indicati in rosso e i numeri con esponente $1$ sono
indicati in verde. Osserviamo l'assenza di numeri di macchina
nell'intervallo $(-1/4,1/4)$, con l'eccezione di $0$.

Il numero positivo più piccolo in $\mathcal{F}_{b,t,m,M}$ è
$r=b^{-m-1}$; lo si ottiene per esponente $-m$ e cifre
$\alpha_1=1, \alpha_2=\ldots =\alpha_t=0$.
`````

Il numero positivo più grande $R$ si ottiene invece scegliendo esponente
$M$ e cifre tutte uguali a $b-1$. Si ha quindi $R=b^M(1-b^{-t})$. Vale
l'inclusione


$$
\mathcal{F}_{b,t,m,M}\subset [-R,-r]\cup \{0\} \cup [r,R].

$$


In formato "double" (doppia precisione), nel quale si usano $64$ bit per
rappresentare ciascun numero: 1 bit per il segno, 11 bit per l'esponente
e 52 bit per la mantissa (cioè il fattore
$\sum_{i=1}^{t}\alpha_i b^{-i}$). Osserviamo che, in base $2$, la prima
cifra $\alpha_1$ deve essere necessariamente 1, perciò non è necessario
rappresentarla esplicitamente.

Errori di troncamento e arrotondamento
======================================

Supponiamo di aver fissato $b$, $t$, $m$ e $M$. Nel seguito poniamo per
semplicità di notazione $\mathcal{F}=\mathcal{F}_{b,t,m,M}$.

Dato $x\in\mathbb{R}$, vogliamo approssimare $x$ con un opportuno
$\tilde{x}\in \mathcal{F}$.

-   Se $|x|>R$, si ha il fenomeno dell'*overflow*.

-   Se $|x|<r$, $x\neq 0$, si ha *underflow*. In questo caso il numero
    viene generalmente posto a zero. Una parziale soluzione al problema
    dell'underflow si ha denormalizzando la mantissa, cioè autorizzando
    una scelta di $\alpha_1,\ldots,\alpha_t$ con $\alpha_1=0$.

-   Se $x\in \mathcal{F}$, si pone $\tilde{x}=x$.

Resta da discutere il caso $r<|x|<R$ con $x\notin \mathcal{F}$.
Supponiamo per semplicità $x>0$; il caso negativo è ovviamente analogo.
Sia

$$
x=b^{\eta}\,\sum_{i=1}^{\infty}\alpha_i b^{-i}

$$
 la
rappresentazione in base di $x$. Abbiamo allora due possibilità per
scegliere $\tilde{x}$:

-   troncamento:


$$
\tilde{x}={\rm trn}(x):=b^{\eta}\,\sum_{i=1}^{t}\alpha_i b^{-i},

$$


-   arrotondamento:


$$
\tilde{x}={\rm arr}(x):=b^{\eta}\,{\rm trn}\left(\sum_{i=1}^{t+1}\alpha_i b^{-i}+\frac12 b^{-t}\right).

$$


In altre parole, sia $r_1={\rm trn}(x)$ e $r_2=r_1+b^{\eta-t}$.
Osserviamo che $r_1$ e $r_2$ sono numeri di macchina consecutivi tali
che $r_1<x<r_2$. Allora si ha

$$
{\rm arr}(x)=\left\{\begin{array}{ll}
r_1 & {\rm se}\,\, x<\frac{r_1+r_2}{2},\\
r_2 & {\rm se}\,\, x\geq\frac{r_1+r_2}{2}.\end{array}\right.

$$
 L'*errore
di rappresentazione* che si commette arrotondando o troncando $x$ è:

-   per il troncamento

$$
\begin{aligned}
    && \left|x-{\rm trn}(x)\right|\leq b^{\eta-t}\qquad {\rm (errore}\,\,{\rm assoluto),}\\
    && \left|\frac{x-{\rm trn}(x)}{x}\right|\leq b^{1-t}\qquad {\rm (errore}\,\,{\rm relativo),}\end{aligned}

$$


-   per l'arrotondamento

$$
\begin{aligned}
    && \left|x-{\rm arr}(x)\right|\leq \frac12 b^{\eta-t}\qquad {\rm (errore}\,\,{\rm assoluto),}\\
    && \left|\frac{x-{\rm arr}(x)}{x}\right|\leq \frac12 b^{1-t}\qquad {\rm (errore}\,\,{\rm relativo).}\end{aligned}

$$


Gli errori assoluti si deducono facilmente dalla definizione; per gli
errori relativi abbiamo usato il fatto che $|x|> b^{\eta-1}$.

La quantità $u:=\frac12 b^{1-t}$ è detta *unit roundoff*, mentre
$b^{1-t}$ è l'*epsilon di macchina* [@higham]. In MATLAB il comando
`eps` restituisce il valore dell'epsilon di macchina per il formato in
doppia precisione, cioè $2^{-52}\approx 2.22\cdot 10^{-16}$.

Aritmetica di macchina
======================

Osserviamo che l'insieme $\mathcal{F}$ non è chiuso rispetto alle
quattro operazioni aritmetiche: la somma (o la differenza, il prodotto,
il quoziente) esatta di due numeri di macchina potrebbe non essere un
numero di macchina. È quindi necessario definire un'aritmetica di
macchina, ovvero delle operazioni binarie $\oplus,\ominus,\otimes,\oslash$,
su $\mathcal{F}$ in modo tale che si abbia

$$\require{enclose}
x \enclose{circle}{\text{op}} y = (1+\varepsilon) (x\, {\rm op}\, y),\qquad |\varepsilon|< u

$$
 per

$$
\require{enclose}
\enclose{circle}{\text{op}}= +,-,\times,/.
$$

```{warning}
L'espressione "floating-point operation" (operazione di macchina) è
spesso abbreviata in *flop*, in particolare quando si stima il costo
computazionale di un algoritmo contando il numero di operazioni
richieste.

Le operazioni di macchina non sempre rispettano le proprietà delle
operazioni classiche, come l'associatività o la distributività. Per
esempio, l'addizione non è necessariamente associativa. Consideriamo in
$\mathcal{F}_{10,2,4,4}$ i numeri

$$
x=0.11\times 10^{0},\qquad
y=0.13\times 10^{-1},\qquad
z=0.14\times 10^{-1}

$$
 e supponiamo che l'addizione di macchina sia
definita da $a\oplus b={\rm arr}(a+b)$. Allora si ha

$$
(x\oplus y)\oplus z = 0.12\times 10^0\oplus 0.14\times 10^{-1} = 0.13\times 10^0

$$
 e

$$
x\oplus (y\oplus z) = 0.11\times 10^0\oplus 0.27\times 10^{-1} = 0.14\times 10^0.

$$
```

Valutazione di una funzione: analisi dell'errore
================================================

Data una funzione

$$
f:D\subset\mathbb{R}^n\longrightarrow\mathbb{R}

$$
 e
fissato $x\in\mathbb{R}^n$, supponiamo di voler approssimare $f(x)$
numericamente.

Sia $\tilde{x}$ la rappresentazione di $x$ in numeri di macchina. Poiché
abbiamo a disposizione solo le quattro operazioni, la funzione $f$ dovrà
essere approssimata da un'opportuna funzione razionale $g$. L'uso delle
operazioni di macchina, poi, fa sı̀ che la funzione effettivamente
calcolata in aritmetica di macchina non sia $g$, ma un'altra funzione
che chiameremo $\psi$.

Avremo quindi i seguenti errori:

-   $\varepsilon$ tale che $\tilde{x}=x(1+\varepsilon)$ e
    $\|\varepsilon\|<u$ *(errore di rappresentazione o roundoff)*,

-   $E_{\rm an}=\frac{f(x)-g(x)}{f(x)}$ *(errore analitico),*

-   $E_{\rm alg}=\frac{g(\tilde{x})-\psi(\tilde{x})}{g(\tilde{x})}$
    *(errore algoritmico),*

-   $E_{\rm in}=\frac{g(x)-g(\tilde{x})}{g(x)}$ *(errore inerente),*

-   $E_{\rm tot}=\frac{f(x)-\psi(\tilde{x})}{f(x)}$ *(errore totale),*

dove naturalmente si suppone che i denominatori siano diversi da zero.

Per esempio, supponiamo di voler valutare $f(x)=e^x$ per un dato
$x\in\mathbb{R}$. Scegliamo come approssimazione razionale
dell'esponenziale la serie di Taylor troncata all'ordine 3, cioè


$$
g(x)=1+x+\frac{x^2}{2}+\frac{x^3}{6}.

$$
 L'errore analitico è allora


$$
E_{\rm an}=\frac{e^x-(1+x+x^2/2+x^3/6)}{e^x}.

$$
 In pratica però non
valuto $g(x)$, bensı̀ $g(\tilde{x})$, da cui l'errore inerente
$E_{\rm in}=(g(x)-g(\tilde{x}))/g(x)$. L'implementazione del calcolo di
$g(\tilde{x})$ tramite le operazioni di macchina dà luogo a


$$
\psi(\tilde{x})=\tilde{1}\oplus\tilde{x}\oplus(\tilde{x}\otimes\tilde{x})\oslash\tilde{2}\oplus((\tilde{x}\otimes\tilde{x})\otimes\tilde{x})\oslash\tilde{6}

$$
 che in generale sarà una quantità diversa da
$g(\tilde{x})$, perciò si ha l'errore algoritmico
$E_{\rm alg}=(g(\tilde{x})-\psi(\tilde{x}))g(\tilde{x})$. Osserviamo
però che avremmo anche potuto scegliere di scrivere $\psi$ in modo
diverso, per esempio

$$
1+x+x(1/2+x/6)

$$
 e avremmo ottenuto un errore
algoritmico ancora diverso.

Ci chiediamo come l'errore totale dipenda dagli altri errori. Si ha


$$
\begin{aligned}
&E_{\rm tot}&=1-\frac{\psi(\tilde{x})}{f(x)}\\
&& = 1-\frac{\psi(\tilde{x})}{g(\tilde{x})}\cdot
\frac{g(\tilde{x})}{g(x)}\cdot\frac{g(x)}{f(x)}\\
&& = 1-(1-E_{\rm alg})(1-E_{\rm in})(1-E_{\rm an})\\
&& \simeq E_{\rm alg}+E_{\rm in}+E_{\rm an},\end{aligned}

$$
 dove
l'ultima uguaglianza è intesa al primo ordine.

Supponiamo ora che $f$ sia razionale, quindi l'errore analitico è nullo.
Sviluppo $f$ in serie di Taylor al primo ordine:


$$
f(\tilde{x})\simeq f(x)+\sum_{i=1}^n
\frac{\partial f}{\partial x_i}(x)(\tilde{x}_i-x_i)

$$
 e indico con
$x_i$, $\tilde{x}_i$, $\varepsilon_i$ le componenti di vettori
$x,\tilde{x},\varepsilon\in\mathbb{R}^n$, rispettivamente. Al primo
ordine si ha

$$
f(\tilde{x}-f(x))\simeq\sum_{i=1}^n
\frac{\partial f}{\partial x_i}(x)(\tilde{x}_i-x_i)
\simeq \sum_{i=1}^n\varepsilon_i x_i\frac{\partial f}{\partial x_i}(x).

$$

L'errore inerente è

$$
-E_{\rm in}\simeq\sum_{i=1}^n\varepsilon_i
\frac{x_i}{f(x)}\frac{\partial f}{\partial x_i}(x)
=\sum_{i=1}^n\varepsilon_i\gamma_i,

$$
 dove i *coefficienti di
amplificazione* $\gamma_i$ sono definiti come
$\gamma_i=\frac{x_i}{f(x)}\frac{\partial f}{\partial x_i}(x)$ per
$i=1,\ldots,n$.

Se i $\gamma_i$ sono piccoli in valore assoluto, allora gli errori di
roundoff su $x$ non vengono amplificati. Invece se i $|\gamma_i|$ sono
grandi potrebbe verificarsi un'amplificazione dell'errore inerente, cioè
si avrebbe un *problema mal condizionato*.

Osserviamo che la discussione dell'errore inerente (e quindi del
condizionamento) non dipende dall'algoritmo scelto per approssimare
$f(x)$, ma solo dal problema, ovvero da $f$ e da $x$.

La crescita dell'errore inerente è legata al *condizionamento* del
problema; la crescita dell'errore algoritmico dipende invece dalla
*stabilità* dell'algoritmo scelto.

Condizionamento delle operazioni di macchina
--------------------------------------------

Come esempio della discussione precedente studiamo il condizionamento
delle operazioni di macchina. Si tratta quindi di valutare l'errore
inerente per ciascuna operazione e di calcolare i coefficienti di
amplificazione $\gamma_i$.

**Addizione.** Supponiamo di voler calcolare $x_1+x_2$. Avremo


$$
\tilde{x}_1=(1+\varepsilon_1)x_1,\qquad
\tilde{x}_2=(1+\varepsilon_2)x_2,

$$
 con $|\varepsilon_1|<u$,
$|\varepsilon_2|<u$, e

$$
\begin{aligned}
E_{\rm in}&=&\frac{(\tilde{x}_1+\tilde{x}_2)-(x_1+x_2)}{x_1+x_2}=\\
&&=\frac{(1+\varepsilon_1)x_1+(1+\varepsilon_2)x_2 -x_1-x_2}{x_1+x_2}=\\
&&=\frac{\varepsilon_1 x_1+\varepsilon_2 x_2}{x_1+x_2}=
\varepsilon_1\frac{x_1}{x_1+x_2}+\varepsilon_2\frac{x_2}{x_1+x_2}.\end{aligned}

$$

Nell'espressione trovata per $E_{\rm in}$, i coefficienti di
amplificazione sono $\gamma_1=x_1/(x_1+x_2)$ e $\gamma_2=x_2/(x_1+x_2)$.
Osserviamo che, se $x_1$ e $x_2$ hanno segno opposto e
$|x_1|\simeq |x_2|$, allora $\gamma_1$ e $\gamma_2$ sono grandi e
l'operazione di addizione è mal condizionata (fenomeno della
*cancellazione* numerica).

**Sottrazione.** Si ha

$$
\begin{aligned}
E_{\rm in}&=&\frac{(\tilde{x}_1-\tilde{x}_2)-(x_1-x_2)}{x_1-x_2}=\\
&&=\frac{(1+\varepsilon_1)x_1-(1+\varepsilon_2)x_2 -x_1+x_2}{x_1-x_2}=\\
&&=\frac{\varepsilon_1 x_1+\varepsilon_2 x_2}{x_1+x_2}=
\varepsilon_1\frac{x_1}{x_1-x_2}+\varepsilon_2\frac{-x_2}{x_1-x_2}.\end{aligned}

$$

I coefficienti di amplificazione sono $\gamma_1=x_1/(x_1-x_2)$ e
$\gamma_2=-x_2/(x_1-x_2)$. Si ha cancellazione, e quindi
malcondizionamento, se $x_1\sim x_2$.

**Moltiplicazione.** Si ha

$$
\begin{aligned}
E_{\rm in}&=&\frac{\tilde{x}_1\tilde{x}_2-x_1x_2}{x_1x_2}=\\
&&=\frac{(1+\varepsilon_1)x_1(1+\varepsilon_2)x_2 -x_1x_2}{x_1x_2}=\\
&&=\frac{\varepsilon_1 x_1 x_2+\varepsilon_2 x_1 x_2}{x_1x_2}=
\varepsilon_1+\varepsilon_2.\end{aligned}

$$
 I coefficienti di
amplificazione sono $\gamma_1=\gamma_2=1$, quindi la moltiplicazione è
sempre ben condizionata.

**Divisione.** Supponiamo che i denominatori che useremo siano diversi
da 0. Si ha


$$
\begin{aligned}
E_{\rm in}&=&\frac{\tilde{x}_1/\tilde{x}_2-x_1/x_2}{x_1/x_2}=\\
&&=\frac{(1+\varepsilon_1)x_1/(1+\varepsilon_2)x_2 -x_1/x_2}{x_1/x_2}=\\
&&=\frac{1+\varepsilon_1 -1- \varepsilon_2}{1+\varepsilon_2}\simeq
\varepsilon_1-\varepsilon_2.\end{aligned}

$$
 Concludiamo che anche la
divisione è sempre ben condizionata.

Referenze
=========
- D. Bini, M. Capovani, O. Menchi, *Metodi numerici per l'algebra
lineare*, Zanichelli 1988. R. Bevilacqua, D. Bini, M. Capovani,
- O. Menchi, *Metodi numerici*, Zanichelli 1992.
- D. Goldberg, *What every
computer scientist should know about floating-point arithmetic.* ACM
computing surveys (CSUR) 23.1 (1991): 5-48.
- Nicholas J. Higham,
*Accuracy and Stability of Numerical Algorithms*, second edition,
Society for Industrial and Applied Mathematics, Philadelphia, PA, USA, 2002.
- IEEE Standard for Floating-Point Arithmetic, IEEE Std 754-2019
(Revision of IEEE 754-2008), The Institute of Electrical and Electronics
Engineers, New York, 2019.
