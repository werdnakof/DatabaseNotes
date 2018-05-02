---


---

<h1 id="logging-and-recovery">Logging and Recovery</h1>
<p>Consider a <strong>disk</strong>, a <strong>buffer</strong>, and a <strong>in-memory transaction</strong></p>
<p>and operations:</p>
<ul>
<li>write(x) - write from memory to buffer</li>
<li>read(x) - read from buffer to memory, or from disk if not present in buffer</li>
<li>output(x) - write to disk from buffer</li>
<li>input(x) - write to buffer from disk</li>
</ul>

<table>
<thead>
<tr>
<th align="center">Action</th>
<th align="center">X</th>
<th align="center">Y</th>
<th align="center"><span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><msub><mi>X</mi><mi>b</mi></msub></mrow><annotation encoding="application/x-tex">X_b</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="strut" style="height: 0.68333em;"></span><span class="strut bottom" style="height: 0.83333em; vertical-align: -0.15em;"></span><span class="base"><span class="mord"><span class="mord mathit" style="margin-right: 0.07847em;">X</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.336108em;"><span class="" style="top: -2.55em; margin-left: -0.07847em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight">b</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"></span></span></span></span></span></span></span></span></span></th>
<th align="center"><span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><msub><mi>Y</mi><mi>b</mi></msub></mrow><annotation encoding="application/x-tex">Y_b</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="strut" style="height: 0.68333em;"></span><span class="strut bottom" style="height: 0.83333em; vertical-align: -0.15em;"></span><span class="base"><span class="mord"><span class="mord mathit" style="margin-right: 0.22222em;">Y</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.336108em;"><span class="" style="top: -2.55em; margin-left: -0.22222em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight">b</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"></span></span></span></span></span></span></span></span></span></th>
<th align="center"><span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><msub><mi>X</mi><mi>d</mi></msub></mrow><annotation encoding="application/x-tex">X_d</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="strut" style="height: 0.68333em;"></span><span class="strut bottom" style="height: 0.83333em; vertical-align: -0.15em;"></span><span class="base"><span class="mord"><span class="mord mathit" style="margin-right: 0.07847em;">X</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.336108em;"><span class="" style="top: -2.55em; margin-left: -0.07847em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight">d</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"></span></span></span></span></span></span></span></span></span></th>
<th align="center"><span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><msub><mi>Y</mi><mi>d</mi></msub></mrow><annotation encoding="application/x-tex">Y_d</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="strut" style="height: 0.68333em;"></span><span class="strut bottom" style="height: 0.83333em; vertical-align: -0.15em;"></span><span class="base"><span class="mord"><span class="mord mathit" style="margin-right: 0.22222em;">Y</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.336108em;"><span class="" style="top: -2.55em; margin-left: -0.22222em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight">d</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"></span></span></span></span></span></span></span></span></span></th>
<th align="center">Log</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">read(x)</td>
<td align="center">20</td>
<td align="center"></td>
<td align="center">20</td>
<td align="center"></td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">x=x-10</td>
<td align="center">10</td>
<td align="center"></td>
<td align="center">20</td>
<td align="center"></td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">write(x)</td>
<td align="center">10</td>
<td align="center"></td>
<td align="center">10</td>
<td align="center"></td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">read(y)</td>
<td align="center">10</td>
<td align="center">50</td>
<td align="center">10</td>
<td align="center">50</td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">y=y+10</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">50</td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">write(y)</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">output(x)</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">output(y)</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center"></td>
</tr>
</tbody>
</table><h3 id="logging">Logging</h3>
<p>main approach to recovering from a system crash relies on a persistent record of changes made during a transaction</p>
<p>3 ways:</p>
<ul>
<li>undo logging</li>
<li>redo logging</li>
<li>undo/redo logging<br>
log records:</li>
<li><strong>start-T</strong>: transaction T has started execution</li>
<li><strong>commit-T</strong>: transaction T has completed successfully and will make not further changes to database items</li>
<li><strong>abort-T</strong>: transaction T could not complete successfully, no changes made by T will be copied to disk</li>
</ul>
<h3 id="undo-logging-rules">Undo Logging Rules</h3>
<ol>
<li>If transaction T modifies database item X, then a log record of the form &lt;T, X, old-value&gt; must be written to disk <strong>before</strong> the new value of X is written to disk</li>
<li>If a transaction T commits, then its &lt;commit-T&gt; log record must be written to disk only after all database items changed by T have been written to disk</li>
</ol>

<table>
<thead>
<tr>
<th align="center">Action</th>
<th align="center">X</th>
<th align="center">Y</th>
<th align="center"><span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><msub><mi>X</mi><mi>b</mi></msub></mrow><annotation encoding="application/x-tex">X_b</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="strut" style="height: 0.68333em;"></span><span class="strut bottom" style="height: 0.83333em; vertical-align: -0.15em;"></span><span class="base"><span class="mord"><span class="mord mathit" style="margin-right: 0.07847em;">X</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.336108em;"><span class="" style="top: -2.55em; margin-left: -0.07847em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight">b</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"></span></span></span></span></span></span></span></span></span></th>
<th align="center"><span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><msub><mi>Y</mi><mi>b</mi></msub></mrow><annotation encoding="application/x-tex">Y_b</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="strut" style="height: 0.68333em;"></span><span class="strut bottom" style="height: 0.83333em; vertical-align: -0.15em;"></span><span class="base"><span class="mord"><span class="mord mathit" style="margin-right: 0.22222em;">Y</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.336108em;"><span class="" style="top: -2.55em; margin-left: -0.22222em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight">b</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"></span></span></span></span></span></span></span></span></span></th>
<th align="center"><span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><msub><mi>X</mi><mi>d</mi></msub></mrow><annotation encoding="application/x-tex">X_d</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="strut" style="height: 0.68333em;"></span><span class="strut bottom" style="height: 0.83333em; vertical-align: -0.15em;"></span><span class="base"><span class="mord"><span class="mord mathit" style="margin-right: 0.07847em;">X</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.336108em;"><span class="" style="top: -2.55em; margin-left: -0.07847em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight">d</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"></span></span></span></span></span></span></span></span></span></th>
<th align="center"><span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><msub><mi>Y</mi><mi>d</mi></msub></mrow><annotation encoding="application/x-tex">Y_d</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="strut" style="height: 0.68333em;"></span><span class="strut bottom" style="height: 0.83333em; vertical-align: -0.15em;"></span><span class="base"><span class="mord"><span class="mord mathit" style="margin-right: 0.22222em;">Y</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.336108em;"><span class="" style="top: -2.55em; margin-left: -0.22222em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight">d</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"></span></span></span></span></span></span></span></span></span></th>
<th align="center">Log</th>
</tr>
</thead>
<tbody>
<tr>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center">&lt;start-T&gt;</td>
</tr>
<tr>
<td align="center">read(x)</td>
<td align="center">20</td>
<td align="center"></td>
<td align="center">20</td>
<td align="center"></td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">x=x-10</td>
<td align="center">10</td>
<td align="center"></td>
<td align="center">20</td>
<td align="center"></td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">write(x)</td>
<td align="center">10</td>
<td align="center"></td>
<td align="center">10</td>
<td align="center"></td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center">&lt;T, X, 20&gt;</td>
</tr>
<tr>
<td align="center">read(y)</td>
<td align="center">10</td>
<td align="center">50</td>
<td align="center">10</td>
<td align="center">50</td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">y=y+10</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">50</td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">write(y)</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">20</td>
<td align="center">50</td>
<td align="center">&lt;T, Y, 50&gt;</td>
</tr>
<tr>
<td align="center">flush log</td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
</tr>
<tr>
<td align="center">output(x)</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">50</td>
<td align="center"></td>
</tr>
<tr>
<td align="center">output(y)</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center">10</td>
<td align="center">60</td>
<td align="center"></td>
</tr>
<tr>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center">&lt;commit-T&gt;</td>
</tr>
<tr>
<td align="center">flush log</td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
<td align="center"></td>
</tr>
</tbody>
</table><h3 id="recovery-with-undo-logging">Recovery with Undo Logging</h3>
<pre><code>	for each log entry &lt;T, X, old&gt;, scanning backwards {
		if &lt;commit T&gt; has been seen {
			do nothing
		} else {
			change the value of X in the database back to old
		}
	}
	for each incomplete transaction T (that was not borted) {
		write &lt;abort T&gt; to log
	}
	flush log
</code></pre>
<h3 id="checkpointing-with-undo-logging">Checkpointing with Undo Logging</h3>
<p>Disadvantage of this approach: we must scan the entire log.<br>
Introduce a periodic checkpoint in the log</p>
<ul>
<li>Before checkpoint, all transactions have committed or aborted</li>
<li>Only need search backwards through the log to the most recent checkpoint</li>
</ul>
<p>New log record type:<br>
&lt;ckpt&gt;: The database has been checkpointed</p>
<p>Checkpointing</p>
<ol>
<li>Stop accepting new transactions</li>
<li>Wait until all active transactions commit/abort and write / to the log</li>
<li>flush log</li>
<li>write  to log</li>
<li>flush log</li>
<li>Resume accepting transactions</li>
</ol>
<h3 id="nonquiescent-checkpointing">Nonquiescent Checkpointing</h3>
<ul>
<li>Need to stop transaction processing while checkpointing (method above)</li>
<li>System may appear to stall</li>
<li>Allow new transactions to enter the system during the checkpoint.</li>
</ul>
<p>New log record types:</p>
<ul>
<li>&lt;start ckpt (T1…Tn)&gt; (Checkpoint starts. T1…Tn are active transactions that<br>
have not yet committed)</li>
<li>&lt;end ckpt&gt;</li>
</ul>
<ol>
<li>Write &lt;start ckpt (T1…Tn)&gt; to log and flush log</li>
<li>Wait until T1…Tn have all committed or aborted</li>
<li>Write  to log and flush log<br>
(Note that new transactions may be started during step 2)</li>
</ol>
<p><strong>Recovery with checkpointed undo logging</strong><br>
Two cases, depending on latest checkpoint log record:</p>
<p>&lt;end ckpt&gt;<br>
All incomplete transactions began after the previous &lt;start ckpt ()&gt;<br>
Disregard the log before the previous &lt;start ckpt ()&gt;</p>
<p>&lt;start ckpt (T1…Tn)&gt;<br>
System crash occurred during checkpoint<br>
Incomplete transactions are those encountered before the &lt;start ckpt ()&gt; and those of T1…Tn that were not committed before the crash<br>
Disregard the log before the start of the earliest incomplete transaction</p>

