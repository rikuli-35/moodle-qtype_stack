# ギリシャ文字アルファベットについて

ギリシャ文字は英語名による転写で入力する。

    [alpha,beta,gamma,delta,epsilon,zeta,eta,theta,iota,kappa,lambda,mu,nu,xi,omicron,pi,rho,sigma,tau,upsilon,phi,chi,psi,omega]
    
大文字のギリシャ文字は、英語名の先頭を大文字にする。

    [Alpha,Beta,Gamma,Delta,Epsilon,Zeta,Eta,Theta,Iota,Kappa,Lambda,Mu,Nu,Xi,Omicron,Pi,Rho,Sigma,Tau,Upsilon,Phi,Chi,Psi,Omega]
    
多くのギリシャ文字は、Maximaですでに特別な意味を持っている。

* `beta`：ベータ関数$\gamma(a)\gamma(b)/\gamma(a+b)$
* `gamma`：ガンマ関数
* `delta`：ディラックデルタ関数（ラプラスでのみ定義）
* `zeta`：リーマンゼータ関数
* `lambda`：ラムダ式（無名関数）を定義して返す
* `psi`: "log (gammma())"の1次導関数で`psi[n](x)` という特殊な構文を持っている。これはテンソルパッケージでも定義されている。

`psi`はデフォルトでは引数を必要としており、引数なしで変数として使うとエラーとなる。このため、STACKではこの関数定義を削除し、`psi`は通常の変数として使えるようにしている。


以下は、STACKによって特定の値が与えられているものである。

* `pi` は円の直径と円周の比を表す数値定数として定義されている。Maximaでは通常`%pi`であるが、STACKでは文字`pi`も同じ値として定義されている。
* Maximaでの黄金比$(1+\sqrt{5})/2$は`%phi`で定義されている。

## Maximaのデフォルトの解除について

現在のところ、Maximaで関数名の定義を未定義に戻して、通常の変数として再利用することはできない。