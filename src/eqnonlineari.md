# Risoluzione numerica di equazioni nonlineari

Ci interessiamo al problema seguente: data una funzione

$$f:[a,b]\subset\mathbb{R}\longrightarrow\mathbb{R},$$

trovare (ovvero,
approssimare numericamente) $x^*\in [a,b]$ tale che $f(x^*)=0$. Si
assume naturalmente che esista almeno un tale $x^*$, detto *zero* di
$f$. Aggiungeremo poi opportune ipotesi di regolarità su $f$ a seconda
dei metodi che tratteremo.

In generale, per problemi di questo tipo si usano metodi iterativi: si
parte da un valore iniziale $x_0$ e si costruisce una successione
$\{x_i\}_{i=0,1,2,\ldots}$ in $[a,b]$ che converga a $x^*$.

# Metodo di bisezione

Il metodo di bisezione sfrutta il *teorema degli zeri*:

````{prf:theorem}
*Sia $f:[a,b]\longrightarrow\mathbb{R}$ una funzione
continua tale che $f(a)f(b)<0$. Allora esiste $x^*\in(a,b)$ tale che
$f(x^*)=0$.*
````

Osserviamo che $x^*$ potrebbe non essere unico. Osserviamo anche che il
teorema dà una condizione sufficiente per l'esistenza di $x^*$, ma tale
condizione non è necessaria.

Supponiamo quindi $f\in C[a,b]$ con $f(a)f(b)<0$. Sia $c_1=(a+b)/2$ il
punto medio del segmento $[a,b]$. Si verifica allora una delle tre
possibilità seguenti:

-   $f(c_1)=0$,

-   $f(a)f(c_1)<0$,

-   $f(c_1)f(b)<0$.

Nel primo caso (possibile ma molto improbabile!) abbiamo trovato
$x^*=c_1$ e quindi il metodo termina. Supponiamo ora che si verifichi
uno dei restanti due casi, per esempio il secondo. Il teorema degli zeri
ci assicura l'esistenza di uno zero di $f$ in $[a,c_1]$: posso quindi
ripetere il ragionamento precedente su $[a,c_1]$, e cosı̀ via.

Più precisamente, siano $a_0=a$, $b_0=b$. Per $i=1,2,\ldots$ definisco

$$c_i=\frac{a_{i-1}+b_{i-1}}{2}.$$

A questo punto:

-   se $f(a_{i-1})f(c_i)<0$, pongo $a_i=a_{i-1}$ e $b_i=c_i$;

-   se $f(c_i)f(b_{i-1})<0$, pongo $a_i=c_i$ e $b_i=b_{i-1}$;

-   se $f(c_i)=0$, ho $x^*=c_i$ e l'algoritmo si arresta.

Poiché ad ogni passo ci si restringe a lavorare su un intervallo la cui
lunghezza è dimezzata rispetto all'intervallo precedente, la successione
$\{c_i\}_{i=1,2,\ldots}$ converge a $x^*$ tale che $f(x^*)=0$.

Per implementare questo metodo è necessario fissare un criterio
d'arresto. Si usa generalmente uno dei seguenti due criteri.

**Criterio sull'ampiezza dell'intervallo.** Fissata una tolleranza
$\varepsilon>0$, si arresta il metodo iterativo quando
$b_i-a_i<\varepsilon$ e scelgo $c_i$ come approssimazione di $x^*$.
Osserviamo che vale

$$|c_i-x^*|<\varepsilon.$$(eq:conv_bisezione)

È possibile dare una stima del numero di passi che si devono fare per
soddisfare questa condizione. All'$i$-esimo passo del metodo di
bisezione ottengo un intervallo di lunghezza $(b-a)/2^i$. Impongo quindi

$$\frac{b-a}{2^i}<\varepsilon,$$

da cui

$$i>\log_2\frac{b-a}{\varepsilon}.$$

In altre parole, se eseguo un
numero di passi superiore a $\log_2((b-a)/\varepsilon)$ ho la certezza
che l'approssimazione trovata per $x^*$ è ad una distanza minore di
$\varepsilon$ dal valore esatto di $x^*$.

**Criterio sul valore di $f(c_i)$.** Fissata una tolleranza
$\varepsilon>0$, il metodo si arresta quando $|f(c_i)|<\varepsilon$, e
si sceglie l'ultimo $c_i$ calcolato come approssimazione di $x^*$.

:::{warning}
A seconda del comportamento della funzione $f$, i due
criteri possono dare risultati sensibilmente diversi.
:::

```{admonition} **Osservazione 2**.
*La proprietà {eq}`eq:conv_bisezione` assicura la *convergenza* del metodo,
ovvero il fatto che, per ogni $\varepsilon>0$ piccolo a piacere, posso
trovare $i$ tale che $|c_i-x^*|<\varepsilon$. Equivalentemente possiamo
dire che $\lim_{i\rightarrow\infty}|c_i-x^*|=0$.*
```

Il metodo di bisezione ha diversi vantaggi: è di semplice
implementazione, assicura sempre la convergenza sotto ipotesi abbastanza
generali, prevede un criterio d'arresto di facile formulazione, richiede
una sola valutazione di $f$ a ciascuna iterazione. Osserviamo infatti
che, in alcune applicazioni, la funzione $f$ potrebbe essere alquanto
costosa da valutare, perciò ai fini del costo computazionale è bene
evitare di effettuare tante valutazioni.

Osserviamo anche che per applicare questo metodo non abbiamo bisogno di
un'espressione esplicita per $f$, ma ci basta saper valutare $f(x)$ per
ogni $x\in[a,b]$ dato.

Il principale inconveniente del metodo di bisezione è la convergenza
lenta. Generalmente questo metodo è usato per dare una prima
approssimazione grossolana della soluzione, che viene poi raffinata
applicando metodi a convergenza più veloce (per es. il metodo di
Newton).

# Ordine di convergenza dei metodi iterativi

Supponiamo di aver definito un metodo iterativo per risolvere $f(x)=0$.
A partire da un punto iniziale $x_0$, il metodo costruisce una
successione $\{x_i\}_{i=0,1,\ldots}$. Ci proponiamo di caratterizzare le
proprietà di convergenza di tale successione ad uno zero $\alpha$ di
$f$.

````{prf:definition}
*Un metodo iterativo si dice *globalmente
convergente* se, per ogni $x_0\in[a,b],$ la successione
$\{x_i\}_{i=0,1,\ldots}$ formata a partire da $x_0$ converge ad $\alpha$
tale che $f(\alpha)=0$.*

*Un metodo iterativo si dice *localmente convergente* se esiste un
intorno $I=[\alpha-\rho,\alpha+\rho]$ di $\alpha$ tale che per ogni
$x_0\in I$ la successione $\{x_i\}_{i=0,1,\ldots}$ formata a partire da
$x_0$ converga ad $\alpha$ tale che $f(\alpha)=0$.*
````

La prossima definizione caratterizza invece la velocità di convergenza
di un metodo iterativo.

````{prf:definition}
*Sia $\{x_i\}_{i=0,1,\ldots}$ una successione
convergente ad $\alpha$ e supponiamo $x_i\neq\alpha$ per ogni $i$. Se
esiste un numero reale $p\geq 1$ tale che

$$\lim_{i\rightarrow\infty}\frac{|x_{i+1}-\alpha|}{|x_i-\alpha|^p}=\gamma,
\qquad {\rm con}\,\,\left\{\begin{array}{ll}
0<\gamma\leq 1 & {\rm se}\,\, p=1,\\
\gamma>0 & {\rm se}\,\, p>1,\end{array}\right.$$

si dice che la
successione ha *ordine di convergenza* $p$, mentre $\gamma$ è detto
fattore di convergenza o costante asintotica.*

*Se $p=1$ e $0<\gamma<1$, si dice che la convergenza è *lineare*; se
$p=1$ e $\gamma=1$, la convergenza è *sublineare*; se $p>1$, si ha
convergenza *superlineare*.*
````

````{prf:definition}
*Un metodo iterativo convergente ad $\alpha$ si dice
*convergente di ordine* $p$ se, per ogni $x_0$ in un intorno di
$\alpha$, la successione prodotta dal metodo è convergente di ordine
$\geq p$, e per almeno una scelta di $x_0$ la successione ha ordine $p$.
Il metodo si dice *convergente di ordine almeno* $p$ se, per ogni $x_0$
in un intorno di $\alpha$, la successione prodotta dal metodo è
convergente di ordine $\geq p$.*
````

La definizione di convergenza di ordine $p$, nei casi lineare e
superlineare, implica che, per $i$ sufficientemente grande, esiste
$\beta>0$ tale che

$$|x_{i+1}-\alpha|\leq\beta|x_{i}-\alpha|^p$$(eq:conv)

con
$\beta<1$ se $p=1$. Questa espressione esprime la riduzione dell'errore
assoluto che avviene a ciascuna iterazione.

:::{admonition} Esempio

Usiamo la relazione
{eq}`eq:conv` per
confrontare il comportamento di un metodo a convergenza lineare e uno a
convergenza quadratica. Supponiamo che valga $|x_0-\alpha|=0,1$ e che la
{eq}`eq:conv` si
possa applicare per $i\geq 0$. Per un metodo a convergenza lineare
(quindi $p=1$) con $\beta=1/2$ si ha la successione di errori assoluti

$$0,1;\,0,05;\,0,025;\,0,0125;\,0,00625;\ldots$$

In altre parole, per
"guadagnare" una cifra decimale (in base 10) sull'accuratezza del
risultato devo eseguire $3$ o $4$ iterazioni, perchè
$(1/2)^3<10^{-1}<(1/2)^4$.

Se invece consideriamo un metodo a convergenza quadratica (quindi
$p=2$) con $\beta=1$, otteniamo la successione di errori assoluti

$$10^{-1};\,10^{-2};\,10^{-4};\,10^{-8};\,10^{-16};\ldots$$

cioè il
numero di cifre significative correttamente individuate raddoppia a
ciascun passo. Bastano quindi $4$ iterazioni per raggiungere un errore
dell'ordine della precisione di macchina.
:::

In certi casi può essere utile adottare una definizione più generale di
ordine di convergenza: si dice che $\{x_i\}_{i=0,1,\ldots}$ è
convergente di ordine $p$ se

$$\max\lim_{i\rightarrow\infty}\frac{|x_{i+1}-\alpha|}{|x_i-\alpha|^p}
\qquad {\rm e} \qquad
\min\lim_{i\rightarrow\infty}\frac{|x_{i+1}-\alpha|}{|x_i-\alpha|^p}$$

sono finiti e non nulli (si chiede anche che siano $\leq 1$ nel caso
$p=1$).

# Metodi di iterazione funzionale

Una tecnica per costruire una successione $\{x_i\}_{i=0,1,\ldots}$ che
converga a $x^*$ tale che $f(x^*)=0$ si basa sull'*iterazione di punto
fisso*. Si introduce a questo scopo un'opportuna funzione $g(x)$ tale
che, se $\beta$ è punto fisso di $g(x)$, cioè

$$\beta=g(\beta)$$

allora
$\beta$ è anche uno zero di $f(x)$. Il punto fisso $\beta$ viene
approssimato per mezzo del metodo iterativo definito da

$$x_{k+1}=g(x_k),\qquad k=0,1,\ldots$$

con un'opportuna scelta del
valore iniziale $x_0$.

Spesso si sceglie $g(x)$ della forma

$$g(x)=x-\frac{f(x)}{h(x)}.$$

````{prf:theorem}
*Supponiamo $g(x)\in C[a,b]$ e sia
$\{x_i\}_{i=0,1,\ldots}$ una successione convergente con limite
$\alpha\in [a,b]$, definita da $x_{k+1}=g(x_k)$. Allora $\alpha$ è punto
fisso di $g$.*
````

````{prf:proof}
Si ha

$$\alpha=\lim_{i\rightarrow\infty}x_{i+1}
= \lim_{i\rightarrow\infty} g(x_i)
= g(\lim_{i\rightarrow\infty} x_i) = g(\alpha),$$

dove abbiamo usato la
continuità di $g$ nella penultima uguaglianza. 
````

````{prf:theorem} Teorema del punto fisso
*Sia $\alpha\in\mathbb{R}$ e
sia $g\in C^1[\alpha-\rho,\alpha+\rho]$, con $\rho>0$, tale che*

-   *$|g'(x)|<1$ per ogni $x\in [\alpha-\rho,\alpha+\rho]$,*

-   *$\alpha=g(\alpha)$.*

*Sia poi $x_0\in[\alpha-\rho,\alpha+\rho]$ e si definisca la successione
$\{x_i\}_{i=0,1,\ldots}$ come*

$$x_{k+1}=g(x_k).$$

*Allora si ha*

$$|x_k-\alpha|\leq\rho, \qquad k=0,1,\ldots$$

e

$$\alpha=\lim_{k\rightarrow} x_k.$$

````

````{prf:proof}
Sia

$$\lambda=\max_{x\in [\alpha-\rho,\alpha+\rho]}|g'(x)|.$$

Poiché vale l'ipotesi $|g'(x)|<1$ e $g'(x)$ è continua su
$[\alpha-\rho,\alpha+\rho]$, si ha $\lambda <1$.

Dimostriamo per induzione la proprietà $|x_i-\alpha|\leq\lambda^i\rho$,
che implica $|x_i-\alpha|\leq\rho$, ovvero la prima parte della tesi.
Per $i=0$, si ha $|x_0-\alpha|\leq\rho$ per ipotesi. Vediamo ora il
passo induttivo. L'ipotesi induttiva è
$|x_{i-1}-\alpha|\leq\lambda^{i-1}\rho$ e vogliamo dimostrare
$|x_{i}-\alpha|\leq\lambda^{i}\rho$. Il teorema del valor medio[^1]
assicura l'esistenza di $\xi_{i-1}\in (\alpha-\rho,\alpha+\rho)$ tale
che

$$x_i-\alpha=g(x_{i-1})-g(\alpha)=g'(\xi_{i-1})(x_{i-1}-\alpha).$$

Passando ai valori assoluti e sfruttando l'ipotesi $|g'(x)|\leq\lambda$
e l'ipotesi induttiva si ha

$$|x_i-\alpha|=|g'(\xi_{i-1})|\, |x_{i-1}-\alpha|
\leq\lambda |x_{i-1}-\alpha|\leq \lambda^i\rho.$$

 Passando al limite per
$i\rightarrow\infty$ si ottiene

$$\lim_{i\rightarrow\infty} |x_i-\alpha|=0.$$ 

````

````{prf:theorem}
*Sia $\alpha$ punto fisso di $g\in C^1[a,b]$. Se
$|g'(x)|<1$ per ogni $x\in [a,b]$, allora $\alpha$ è l'unico punto fisso
di $g$ in $[a,b]$.*
````

````{prf:proof}
Supponiamo per assurdo che esista in $[a,b]$ un secondo punto
fisso $\beta$ per $g$, distinto da $\alpha$. Allora per il teorema del
valor medio esiste $\xi\in [a,b]$ tale che

$$|\alpha-\beta|=|g(\alpha)-g(\beta)|=|g'(\xi)|\,|\alpha-\beta|<|\alpha-\beta|.$$

Concludiamo che si ha $\alpha=\beta$. 
````

# Metodo di Newton

Partiamo dalle consuete ipotesi $f:[a,b]\rightarrow\mathbb{R}$,
$f(\alpha)=0$, e supponiamo inoltre che la funzione $f(x)$ sia di classe
$C^1$ in un intorno $I$ dello zero $\alpha$, e che valga $f'(x)\neq 0$
per ogni $x\in I\setminus \{\alpha\}$. Fissato il punto iniziale $x_0$,
il *metodo di Newton* è definito da

$$x_{k+1}=x_k-\frac{f(x_k)}{f'(x_k)}.$$

Questo metodo è anche detto
*metodo delle tangenti*, perché $x_{k+1}$ può essere visto come
l'ascissa del punto di intersezione tra la tangente al grafico di $f$
nel punto $(x_k,f(x_k))$ e l'asse delle $x$.

Che cosa possiamo dire sulle proprietà di convergenza?

````{prf:definition}
*Sia $f\in C^r[a,b]$ e sia $\alpha\in (a,b)$ tale
che $f(\alpha)=0$. Si dice che $\alpha$ ha *molteplicità* $r$ come zero
di $f$ se esiste finito e non nullo il limite
$\lim_{x\rightarrow\alpha}\frac{f(x)}{(x-\alpha)^r}\neq 0$, cioè se vale
````


````{prf:theorem} Convergenza Newton
:label: conv_newton

 *Sia $\alpha\in [a,b]$ soluzione di $f(x)=0$, con
$f'(x)\neq 0$ per ogni $x\in[a,b]\setminus \{\alpha\}$.*

1.  *Se $\alpha$ ha molteplicità $1$ e $f(x)\in C^2[a,b]$, allora il
    metodo di Newton è localmente convergente con ordine almeno $2$. In
    particolare, l'ordine di convergenza è esattamente $2$ se
    $f''(\alpha)\neq 0$.*

2.  *Se $\alpha$ ha molteplicità finita $r\geq 2$ e $f(x)\in C^r[a,b]$,
    allora il metodo di Newton è localmente convergente con ordine $1$ e
    costante asintotica $1-\frac{1}{r}$.*
````

````{prf:proof}
Dividiamo la dimostrazione in due passi

1.  Poiché $\alpha$ ha molteplicità $1$, vale $f'(\alpha)\neq 0$.
    Osserviamo inoltre che, con la notazione introdotta in precedenza,
    la funzione

    $$g(x)=x-\frac{f(x)}{f'(x)}$$

    è di classe $C^1$ su
    $[a,b]$, quindi ha derivata prima continua. Si ha

    $$g'(\alpha)=\frac{f(\alpha)f''(\alpha)}{f'(\alpha)^2}=0,$$

    perciò
    esiste un intorno in $\alpha$ in cui vale $|g'(x)|<1$. Si deduce
    quindi la convergenza locale del metodo.

    Per determinare l'ordine di convergenza, sviluppo $f(x)$ in serie di
    Taylor in un intorno di $x_k$. Esiste $\xi\in [a,b]$ con
    $|\xi-k_k|<|\alpha-x_k|$ tale che

    $$0=f(\alpha)=f(x_k)+(\alpha-x_k)f'(x_k)+\frac12 (\alpha-x_k)^2f''(\xi).$$(eq:taylor1)

    Osservo che si ha

    $$f(x_k)+(\alpha-x_k)f'(x_k)
    = f'(x_k)\left[\frac{f(x_k)}{f'(x_k)}-x_k+\alpha\right]
    = f'(x_k)[-x_{k+1}+\alpha].$$

    Sostituendo in
    {eq}`eq:taylor1` si ottiene

    $$f'(x_k)(x_{k+1}-\alpha)=\frac12 (\alpha-x_k)^2 f''(\xi),$$
    da cui

    $$\frac{x_{k+1}-\alpha}{(x_k-\alpha)^2}=\frac{f''(\xi)}{2f'(x_k)}.$$

    Passando al limite, nell'ipotesi $f''(\alpha)\neq 0$ si ha

    $$\lim_{k\rightarrow\infty}\frac{x_{k+1}-\alpha}{(x_k-\alpha)^2}=
    \frac{f''(\xi)}{2f'(\alpha)}\neq 0,$$

    quindi l'ordine di convergenza
    è 2. Se invece $f''(\alpha)= 0$ e $f(x)\in C^3[a,b]$, allora
    l'ordine di convergenza è $\geq 3$.

2.  Ci limitiamo a dimostrare che l'ordine di convergenza è lineare (non
    dimostriamo la convergenza locale). Sviluppiamo $f(x_k)$ e $f'(x_k)$
    in serie di Taylor in un intorno di $\alpha$. Per l'ipotesi sulla
    molteplicità sappiamo
    $f(\alpha)=f'(\alpha)=\ldots=f^{(r-1)}(\alpha)=0$ e
    $f^{(r)}(\alpha)\neq 0$. Si ha quindi

    $$f(x_k)=\frac{(x_k-\alpha)^r}{r!}f^{(r)}(\xi_k),\qquad
    f'(x_k)=\frac{(x_k-\alpha)^{r-1}}{(r-1)!}f^{(r)}(\eta_k),$$

    per
    opportuni valori $\xi_k,\eta_k$ compresi tra $x_k$ e $\alpha$. Di
    conseguenza l'iterazione di Newton si può scrivere come

    $$x_{k+1}=x_k-\frac{f(x_k)}{f'(x_k)}=x_k-\frac{(x_k-\alpha)f^{(r)}(\xi_k)}{rf^{(r)}(\eta_k)}$$

    da cui

    $$x_{k+1}-\alpha =x_k-\alpha-\frac{(x_k-\alpha)f^{(r)}(\xi_k)}{rf^{(r)}(\eta_k)}=(x_k-\alpha)\left(1-\frac{f^{(r)}(\xi_k)}{rf^{(r)}(\eta_k)}\right)$$

    e passando al limite per $k\rightarrow \infty$ si ottiene

    $$\lim_{k\rightarrow \infty}\frac{x_{k+1}-\alpha}{x_k-\alpha}=1-\frac{1}{r}.$$

````

Abbiamo osservato che in generale il metodo di Newton è localmente
convergente. In alcuni casi è possibile dare condizioni sufficienti di
convergenza su intervalli, per esempio sotto opportune ipotesi di
convessità. Enunciamo ora un criterio di questo tipo.

````{prf:theorem}
*Sia $f\in C^2[\alpha,\alpha+\rho]$ con $\rho>0$,
$f(\alpha)=0$. Supponiamo che valga $f(x)f''(x)>0$ e $f'(x)\neq 0$ per
ogni $x\in (\alpha,\alpha+\rho]$. Allora, per qualunque valore iniziale
$x_0\in (\alpha,\alpha+\rho]$, la successione generata dal metodo di
Newton è decrescente e convergente ad $\alpha$.*
````

Analogamente si ha

````{prf:theorem}
 *Sia $f\in C^2[\alpha-\rho,\alpha]$ con $\rho>0$,
$f(\alpha)=0$. Supponiamo che valga $f(x)f''(x)>0$ e $f'(x)\neq 0$ per
ogni $x\in [\alpha-\rho,\alpha)$. Allora, per qualunque valore iniziale
$x_0\in [\alpha-\rho,\alpha)$, la successione generata dal metodo di
Newton è crescente e convergente ad $\alpha$.*
````

:::{admonition} Osservazione
Nel caso in cui $\alpha$ abbia molteplicità $r>1$
e il metodo di Newton abbia quindi convergenza lineare, si introduce il
*metodo di Newton modificato*, definito dall'iterazione

$$x_{k+1}=x_k-r\frac{f(x_k)}{f'(x_k)}$$

che ha convergenza almeno
quadratica in un intorno di $\alpha$. (Si può verificare che l'ordine di
convergenza è $2$ se $f^{(r+1)}(\alpha)\neq 0$, con un ragionamento
simile alla dimostrazione del {prf:ref}`conv_newton`.
:::

# Criteri di arresto

Idealmente vorremmo arrestare un metodo iterativo $x_{k+1}=g(x_k)$
quando $|x_k-\alpha|<\varepsilon$, dove $\varepsilon>0$ è una tolleranza
prefissata. Naturalmente però non sappiamo quanto valga l'errore di
approssimazione $|x_k-\alpha|$, perché non conosciamo $\alpha$. Dobbiamo
quindi usare criteri basati su quantità che sappiamo calcolare.

I due criteri principali usati per un metodo iterativo sono:

-   il criterio del residuo:

$$|f(x_k)|<\varepsilon,$$

-   il criterio dell'incremento:

$$|x_{k+1}-x_k|<\varepsilon.$$

Supponiamo di applicare il criterio del residuo, con $f(x)$ di classe
$C^1$. Dal teorema del valor medio si ha

$$f(x_k)=f(\alpha)+(x_k-\alpha)f'(\xi_k),\qquad {\rm con}\,\, |\xi_k-\alpha|<|x_k-\alpha|,$$

e, poiché $f(\alpha)=0$, otteniamo

$$|x_k-\alpha|=\frac{f(x_k)}{f'(\xi_k)}<
\frac{\varepsilon}{f'(\xi_k)}\simeq \frac{\varepsilon}{f'(\alpha)},$$

dove l'ultima approssimazione è valida per $k$ sufficientemente grande.
Abbiamo quindi tre casi:

1.  se $|f'(\alpha)|$ è vicino a 1, allora ho una buona stima
    dell'errore assoluto di approssimazione;

2.  se $|f'(\alpha)|\ll 1$, allora l'errore assoluto di approssimazione
    potrebbe essere molto più grande di $\varepsilon$, quindi rischiamo
    di avere un'approssimazione poco accurata;

3.  se $|f'(\alpha)|\gg 1$, allora l'errore assoluto di approssimazione
    è molto più piccolo di $\varepsilon$; stiamo usando un criterio
    troppo restrittivo, che ci costringe ad usare più iterazioni del
    necessario.

Per il metodo di Newton si usa generalmente il metodo dell'incremento. A
giustificazione di questo criterio, osserviamo che si ha

$$x_{k+1}-x_k=-\frac{f(x_k)}{f'(x_k)}$$

$$|x_{k+1}-x_k|=\frac{|f(x_k)|}{|f'(x_k)|}
\simeq\frac{|f(x_k)|}{|f'(\xi_k)|}=|x-\alpha|,$$

dove l'ultima
uguaglianza viene dal teorema del valor medio, mentre l'approssimazione
si intende per $k$ sufficientemente grande.

# Altri metodi iterativi

Il **metodo delle corde** è definito dall'iterazione

$$x_{k+1}=x_k-\frac{f(x_k)}{m},\qquad k=0,1,\ldots.$$

Geometricamente
questo significa che $x_{k+1}$ è l'ascissa del punto di intersezione tra
l'asse delle $x$ e la retta passante per $(x_k,f(x_k))$ con coefficiente
angolare $m$.

Se $f(x)\in C^1[a,b]$, possiamo applicare il teorema del punto fisso e
concludere che la successione definita dal metodo delle corde è
convergente se

$$|g'(x)|=\left|1-\frac{f'(x)}{m}\right|<1$$

in un
intorno $I$ di $\alpha$, nel quale supponiamo di aver scelto il punto
iniziale $x_0$. Si deducono quindi le seguenti condizioni sufficienti
per la convergenza:

$$\begin{aligned}
&&f'(x)\neq 0 \,\,{\rm per}\,x\in I, \\
&& mf'(x)>0,\\
&&|m|>\frac12\max_{x\in I}|f'(x)|.
\end{aligned}$$

Si dimostra che la convergenza è lineare, tranne quando
$m=f'(\alpha)$, nel qual caso ha ordine $2$. È sufficiente una sola
valutazione di $f(x)$ per iterazione.

Il **metodo delle secanti** è definito dall'iterazione

$$x_{k+1}=x_k-f(x_k)\frac{x_k-x_{k-1}}{f(x_k)-f(x_{k-1})},\qquad k=1,2,\ldots,$$

dove si suppone di aver fissato $x_0$ e $x_1$.

Geometricamente questo significa che $x_{k+1}$ è l'ascissa del punto di
intersezione tra l'asse delle $x$ e la retta (secante) passante per i
punti di coordinate $(x_{k-1},f(x_{k-1}))$ e $(x_k,f(x_k))$.

Si dimostra che, per $f\in C^2[a,b]$ e $f'(\alpha)\neq 0$, vale la
convergenza locale con ordine $\frac{1+\sqrt{5}}{2}\approx 1,618$. Ad
ogni iterazione è sufficiente una sola valutazione di $f(x)$, come per
il metodo delle corde e per il metodo di bisezione.

# Bibliografia

- D. Bini, M. Capovani, O. Menchi, *Metodi numerici per l'algebra
lineare*, Zanichelli 1988.
- R. Bevilacqua, D. Bini, M. Capovani,
O. Menchi, *Metodi numerici*, Zanichelli 1992.

[^1]: *Teorema del valor medio o di Lagrange:* sia $f(x)$ funzione
    continua su $[a,b]$ e derivabile su $(a,b)$; allora esiste
    $\xi\in(a,b)$ tale che $f(b)-f(a)=(b-a)f'(\xi)$.
