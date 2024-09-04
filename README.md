
# SimRelata

Plur-Contextual Social Simulation Framework

## 基本定義

1. $\Sigma$ を有限のシンボルの集合とする。

2. $Val=\Sigma^{*n}\cup\Z^n\cup\R^n\cup\{\mathrm {T,F}\}^n$ を値の集合とする。

3. $Var=\{\text{name}\to v\mid \text{name}\in\Sigma^*,~v\in Val\}$ を変数の集合とする。

---
### クラスとスキーマ

1. $C=\{\text{Agent},\text{Spot}, \text{AgentSet}, \text{SpotSet}, \text{Custom}\}$ をクラスの集合とする。

2. $Cd=\{\text{OneToOne}, \text{OneToMany}, \text{ManyToOne}, \text{ManyToMany}\}$ を関係性の多重度の集合とする。

3. スキーマを以下のように定義する:

$$S:\Sigma^*\to (Cd\times C\times C)$$
$$S(\text{relation\_name})=(\text{cardinality}, \text{class1}, \text{class2})$$

Where:
- $\text{relation\_name}\in\Sigma^*$
- $\text{cardinality}\in Cd$
- $\text{class1} \in C$
- $\text{class2} \in C$

### エンティティ

$E$ をエンティティの集合とし、以下のように定義する:

$$E = \text{UUID} \times \Sigma^* \times C \times Var \times Var \times 2^R \times 2^F$$

各エンティティ $e \in E$ は次のように表される：

$$e = (\text{id}, \text{name}, \text{class}, \text{attributes}, \text{states}, \text{relations}, \text{facets})$$

Where:
- $\text{id} \in \text{UUID}$（システムによって生成される一意の識別子）
- $\text{name} \in \Sigma^*$
- $\text{class} \in C$
- $\text{attributes}, \text{states} \in Var$
- $\text{relations} \in 2^R$（後述:関係）
- $\text{facets} \in 2^F$（後述:ファーセット）


### 関係
$R$ を関係の集合とし、以下のように定義する：

$$R = \text{UUID} \times \Sigma^* \times Cd \times E \times E \times Var$$

各関係 $r \in R$ は次のように表される：
$$r = (\text{id}, \text{name}, \text{cardinality}, \text{entity1}, \text{entity2}, \text{information})$$

Where:
- $\text{id} \in \text{UUID}$
- $\text{name} \in \Sigma^*$
- $\text{cardinality} \in Cd$
- $\text{entity1}, \text{entity2} \in E$
- $\text{information} \in Var$

ここで、以下の制約が満たされる：

$$\forall r \in e.\text{relations}, r.\text{entity1} = e \lor r.\text{entity2} = e$$
$$S(r.\text{name}).\text{cardinality} = r.\text{cardinality}$$
$$S(r.\text{name}).\text{class1} = r.\text{entity1}.\text{class}$$
$$S(r.\text{name}).\text{class2} = r.\text{entity2}.\text{class}$$


### ファーセット
$F$ をファーセットの集合とし、以下のように定義する：

$$F = \text{UUID} \times \Sigma^* \times E \times Var \times 2^P\times\{\text{T, F}\}$$

各ファーセット $f \in F$ は次のように表される：

$$f = (\text{id}, \text{name}, \text{entity}, \text{states}, \text{processes}, \text{active})$$

Where:
- $\text{id} \in \text{UUID}$
- $\text{name} \in \Sigma^*$
- $\text{entity} \in E$
- $\text{states} \in Var$
- $\text{processes} \subseteq 2^P$
- $\text{active} \in \{\text{T, F}\}$

ここで、以下の制約が満たされる：

$$^\forall f \in e.\text{facets}, f.\text{entity} = e$$
$$\bigcup_{f \in e.\text{facets}} f.\text{states} = e.\text{states}$$

---
### プロセス

$P$ をプロセスの集合とし、以下のように定義する：

$$P = \text{UUID} \times \Sigma^* \times F \times (\mathcal{C} \to 2^\mathcal{E})$$

各プロセス $p \in P$ は次のように表される：

$$p = (\text{id}, \text{name}, \text{facet}, \text{execute})$$

Where:
- $\text{id} \in \text{UUID}$
- $\text{name} \in \Sigma^*$
- $\text{facet} \in F$
- $\text{execute}: \mathcal{C} \to 2^\mathcal{E}$
- $\mathcal{C}$ （後述:コンテキスト）
- $\mathcal{E}$ （後述:エフェクト）

ここで、以下の制約が満たされる：

$$^\forall p\in f.\text{processes}, p.\text{facet} = f$$

### スケジュール

1. $\text{Phases}$ をフェーズの集合とする。

2. スケジュールを以下のように定義する：

$$\text{Schedule} = \text{Phases} \times \N \times \N \times \N \times (\mathcal{C} \to {\mathrm{T, F}})$$

$$\text{schedule} = (\text{start}, \text{phases}, \text{span}, \text{end}, \text{criterion})$$

Where:
- $\text{start} \in \N$ （開始時間）
- $\text{phases} \in \text{Phases}$ （フェーズ）
- $\text{span} \in \N$ （繰り返し間隔）
- $\text{end} \in \N$ （終了時間）
- $\text{criterion}: \mathcal{C} \to \{\mathrm{T, F}\}$ （コンテキストによって評価される条件）

（＊）開始時間は、開始時刻ではなく、スケジュール後から実行までの時間を表す。

### コンテキスト

$\mathcal{C}$ をコンテキストの集合とし、以下のように定義する：

$$\mathcal{C} = \N\times\text{Phases}\times E$$

各コンテキスト $c \in \mathcal{C}$ は次のように表される：

$$c = (\text{time},\text{phase}, \text{entity})$$

Where:
- $\text{time} \in \N$
- $\text{phase} \in \text{Phases}$
- $\text{entity} \in E$

ここで、以下の制約が満たされる：

- $^\forall p\in f.\text{processes}, ^\forall f \in e.\text{facets}$ について、プロセス $p$ の $\text{execute}$ 関数は、エンティティ $e$ を含むコンテキスト $c$ に対して実行される。


#### コンテキストの操作

コンテキストは$\text{execute}$関数の内部で読み取り専用の情報を提供する。

$$c\in\mathcal{C},\left\{\begin{array}{l}
    c.\text{getTime}() \to \N \\
    c.\text{getPhase}() \to \text{Phases} \\
    c.\text{getSelf}() \to E \\
\end{array}\right.$$
$$e\in E,\left\{\begin{array}{l}
    e.\text{getName}() \to \Sigma^* \\
    e.\text{getClass}() \to C \\
    e.\text{getAttribute}(\text{name}) \to Var \\
    e.\text{getState}(\text{name}) \to Var \\
    e.\text{getRelation}(\text{name}) \to 2^R \\
    e.\text{getFacet}(\text{name}) \to F \\
    e.\text{getRelated}(\text{name}) \to 2^E \\
    e.\text{hasAttribute}(\text{name}) \to \{\text{T, F}\} \\
    e.\text{hasState}(\text{name}) \to \{\text{T, F}\} \\
    e.\text{hasRelation}(\text{name}) \to \{\text{T, F}\} \\
    e.\text{hasFacet}(\text{name}) \to \{\text{T, F}\} \\
\end{array}\right.$$
$$r\in R,\left\{\begin{array}{l}
    r.\text{getId}() \to \text{UUID} \\
    r.\text{getName}() \to \Sigma^* \\
    r.\text{getCardinality}() \to Cd \\
    r.\text{getOther}(e) \to E \\
    r.\text{getInformation}(\text{name}) \to Var \\
    r.\text{hasInformation}(\text{name}) \to \{\text{T, F}\} \\
\end{array}\right.$$
$$f\in F,\left\{\begin{array}{l}
    f.\text{getName}() \to \Sigma^* \\
    f.\text{isActive}() \to \{\text{T, F}\} \\
    f.\text{getProcesses}() \to 2^P \\
    f.\text{hasProcess}(\text{name}) \to \{\text{T, F}\} \\
\end{array}\right.$$
$$p\in P,\left\{\begin{array}{l}
    p.\text{getName}() \to \Sigma^* \\
    p.\text{getSchedule}() \to \text{Schedule} \\
\end{array}\right.$$
$$s\in\text{Schedule},\left\{\begin{array}{l}
    s.\text{getStart}() \to \N \\
    s.\text{getPhases}() \to \text{Phases} \\
    s.\text{getSpan}() \to \N \\
    s.\text{getEnd}() \to \N \\
    s.\text{getCriterion}() \to (\mathcal{C} \to \{\mathrm{T, F}\}) \\
\end{array}\right.$$

### エフェクト

$\mathcal{E}$ をエフェクトの集合とし、以下のように定義する：

$$\displaystyle \mathcal{E}=\left\{\begin{array}{l}
    \text{DeleteEntity}=(\text{entity\_id}) \\
    \text{RemoveRelation}=(\text{relation\_id}) \\
    \text{RemoveInformation}=(\text{relation\_id}, \text{information\_name}) \\
    \text{RemoveFacet}=(\text{entity\_id}, \text{facet\_name}) \\
    \text{DeactivateFacet}=(\text{entity\_id}, \text{facet\_name}) \\
    \text{CancelProcess}=(\text{entity\_id}, \text{facet\_name}, \text{process\_name}) \\
    \text{CreateEntity}=(\text{name}, \text{class}, \text{attributes}, \{\text{AddRelation}\}, \{\text{AddFacet}\}) \\
    \text{AddRelation}=(\text{entity\_id}, \text{relation\_name}, \{\text{AddInformation}\}) \\
    \text{AddInformation}=(\text{relation\_id}, \text{information\_name}, \text{value}) \\
    \text{AddFacet}=(\text{entity\_id}, \text{facet\_name}, \{\text{ChangeState}\}, \text{ActivateFacet}) \\
    \text{ChangeState}=(\text{entity\_id}, \text{state\_name}, \text{value}) \\
    \text{ActivateFacet}=(\text{entity\_id}, \text{facet\_name}, \{\text{ScheduleProcess}\}) \\
    \text{ScheduleProcess}=(\text{entity\_id}, \text{facet\_name}, \text{process\_name}, \text{schedule}) \\
\end{array}\right\} $$

ここで、以下の制約が満たされる：

- $\text{entity\_id} \in \text{UUID}$ は自身のIDのみを指定する。

- $\text{relation\_id} \in \text{UUID}$ は自身の関係のIDのみを指定する。

各エフェクトの説明：

1. DeleteEntity: 自身を削除する。
2. RemoveRelation: 指定されたIDの関係を自身から削除する。
3. RemoveInformation: 指定された関係IDから特定の情報を削除する。
4. RemoveFacet: 指定された名前のファーセットを自身から削除する。
5. DeactivateFacet: 指定された名前のファーセットを非アクティブにする。
6. CancelProcess: 指定されたIDのプロセスのスケジュールをキャンセルする。
7. CreateEntity: 新しいエンティティを作成する。名前、クラス、属性を設定し、同時に関係の追加とファーセットの追加も行える。
8. AddRelation: 自身と指定されたエンティティIDとの間に新しい関係を追加する。同時に情報の追加も行える。
9. AddInformation: 指定された関係IDに新しい情報を追加する。
10. AddFacet: 自身に新しいファーセットを追加する。同時に状態の変更、ファーセットのアクティブ化を行える。
11. ChangeState: 自身の指定された名前の状態を変更する。
12. ActivateFacet: 指定された名前のファーセットをアクティブにする。同時にプロセスのスケジューリングも行える。
13. ScheduleProcess: 指定された名前のプロセスをスケジュールする。

これらのエフェクトは、コンテキストから取得した読み取り専用の情報を基にユーザーが定義した$\text{execute}$関数によって生成され、システムの状態を変更するために使用される。

---
### モデリング&シミュレーション

モデル $M$ を以下のように定義する：

$$M = (C, E, R, F, P, S, \text{Phases}, \text{ActiveProcesses}, \text{ProcessQueue})$$

ここで：
- $C$: クラスの集合
- $E$: エンティティの集合
- $R$: 関係の集合
- $F$: ファーセットの集合
- $P$: プロセスの集合
- $S$: スキーマ関数
- $\text{Phases}$: フェーズの集合
- $\text{ActiveProcesses}: \N \times \text{Phases} \to E \to P$
  現在のタイムステップとフェーズで実行されるプロセスとエンティティのマッピング
- $\text{ProcessQueue}: \text{PriorityQueue}((\N, E, P, \text{Schedule}))$
  優先度付きキューで管理される将来のプロセス情報

ある時刻 $t$ におけるシミュレーションの状態 $\text{State}$ を以下のように定義する：

$$\text{State}(t) = (E_t, R_t, F_t, \text{ActiveProcesses}_t, \text{ProcessQueue}_t)$$

シミュレーションの進行関数を以下のように定義する：

$$\text{Simulate}: \text{State}(t) \to \text{State}(t+1)$$

この関数は以下のステップで実行される：

1. 各フェーズごとにプロセスを実行し、エフェクトを生成して適用する：

$$\begin{aligned}
\text{ExecutePhase}: & \text{State}(t) \times \text{Phases} \to \text{State}(t) \\
\text{ExecutePhase}(\text{State}(t), \phi) = & \text{ApplyEffects}(\text{State}(t), \text{ExecuteProcesses}(\text{State}(t), \phi))
\end{aligned}$$

ここで：

$$\begin{aligned}
\text{ExecuteProcesses}: & \text{State}(t) \times \text{Phases} \to 2^\mathcal{E} \\
\text{ExecuteProcesses}(\text{State}(t), \phi) = & \bigcup_{(e, p) \in \text{ActiveProcesses}_t(t, \phi)} p.\text{execute}(c_{t,\phi,e})
\end{aligned}$$

$c_{t,\phi,e} = (t, \phi, e) \in \mathcal{C}$　はコンテキストを表す。

$$\text{ApplyEffects}: \text{State}(t) \times 2^\mathcal{E} \to \text{State}(t)$$

2. すべてのフェーズを順番に実行する：

$$\text{State}'(t) = \text{ExecuteAllPhases}(\text{State}(t))$$

ここで：

$$\begin{aligned}
\text{ExecuteAllPhases}: & \text{State}(t) \to \text{State}(t) \\
\text{ExecuteAllPhases}(\text{State}(t)) = & (\text{ExecutePhase} \circ \cdots \circ \text{ExecutePhase})(\text{State}(t), \phi_1, \ldots, \phi_n) \\
& \text{where } \{\phi_1, \ldots, \phi_n\} = \text{Phases} \text{ in order}
\end{aligned}$$

3. $\text{ProcessQueue}_t$ から次のタイムステップ $(t+1)$ のプロセスを $\text{ActiveProcesses}_{t+1}$ に移動する：

$$\begin{aligned}
\text{UpdateActiveProcesses}: & \text{State}(t) \to \text{State}(t+1) \\
\text{UpdateActiveProcesses}(\text{State}'(t)) = & \text{State}(t+1) \text{ where } \\
& \text{ActiveProcesses}_{t+1}(t+1, \phi) = \{(e, p) \mid (t+1, e, p, s) \in \text{ProcessQueue}_t, \phi \in s.\text{phases}\} \\
& \text{ProcessQueue}_{t+1} = \text{ProcessQueue}_t \setminus \{(t+1, e, p, s) \mid (e, p) \in \text{ActiveProcesses}_{t+1}(t+1, \phi)\}
\end{aligned}$$

4. 繰り返しスケジュールの場合、次回の実行時間を計算し $\text{ProcessQueue}_{t+1}$ に追加する：

$$\begin{aligned}
\text{RescheduleProcesses}: & \text{State}(t+1) \to \text{State}(t+1) \\
\text{RescheduleProcesses}(\text{State}(t+1)) = & \text{State}(t+1) \text{ where } \\
& \text{ProcessQueue}_{t+1} = \text{ProcessQueue}_{t+1} \cup \{(t_\text{next}, e, p, s) \mid (e, p) \in \text{ActiveProcesses}_{t+1}(t+1, \phi), \\
& \qquad s.\text{end} > t+1, t_\text{next} = t+1+s.\text{span}\}
\end{aligned}$$

したがって、シミュレーションの1ステップは以下のように表現できます：

$$\text{Simulate}(\text{State}(t)) = \text{RescheduleProcesses}(\text{UpdateActiveProcesses}(\text{ExecuteAllPhases}(\text{State}(t))))$$

プロセスのスケジューリング関数を以下のように定義する：

$$\text{ScheduleProcess}: \text{State}(t) \times E \times P \times \text{Schedule} \to \text{State}(t)$$

この関数は、指定されたエンティティ、プロセス、スケジュール情報に基づいて $\text{ActiveProcesses}$ と $\text{ProcessQueue}$ を更新する：

$$\begin{aligned}
\text{ScheduleProcess}(\text{State}(t), e, p, s) = & \text{State}(t) \text{ where } \\
& \text{if } s.\text{start} = 0: \\
& \quad \text{ActiveProcesses}_t(t, \phi) = \text{ActiveProcesses}_t(t, \phi) \cup \{(e, p)\} \text{ for } \phi \in s.\text{phases} \\
& \text{else}: \\
& \quad \text{ProcessQueue}_t = \text{ProcessQueue}_t \cup \{(t+s.\text{start}, e, p, s)\}
\end{aligned}$$
