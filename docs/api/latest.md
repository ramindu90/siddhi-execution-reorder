# API Docs - v5.0.3

!!! Info "Tested Siddhi Core version: *<a target="_blank" href="http://siddhi.io/en/v5.0/docs/query-guide/">5.0.2</a>*"
    It could also support other Siddhi Core minor versions.

## Reorder

### akslack *<a target="_blank" href="http://siddhi.io/en/v5.0/docs/query-guide/#stream-processor">(Stream Processor)</a>*
<p style="word-wrap: break-word">Stream processor performs reordering of out-of-order events optimized for a givenparameter using [AQ-K-Slack algorithm](http://dl.acm.org/citation.cfm?doid=2675743.2771828). This is best for reordering events on attributes those are used for aggregations.data .</p>
<span id="syntax" class="md-typeset" style="display: block; font-weight: bold;">Syntax</span>

```
reorder:akslack(<LONG> timestamp, <INT|FLOAT|LONG|DOUBLE> correlation.field)
reorder:akslack(<LONG> timestamp, <INT|FLOAT|LONG|DOUBLE> correlation.field, <LONG> batch.size)
reorder:akslack(<LONG> timestamp, <INT|FLOAT|LONG|DOUBLE> correlation.field, <LONG> batch.size, <LONG> timeout)
reorder:akslack(<LONG> timestamp, <INT|FLOAT|LONG|DOUBLE> correlation.field, <LONG> batch.size, <LONG> timeout, <LONG> max.k)
reorder:akslack(<LONG> timestamp, <INT|FLOAT|LONG|DOUBLE> correlation.field, <LONG> batch.size, <LONG> timeout, <LONG> max.k, <BOOL> discard.late.arrival)
reorder:akslack(<LONG> timestamp, <INT|FLOAT|LONG|DOUBLE> correlation.field, <LONG> batch.size, <LONG> timeout, <LONG> max.k, <BOOL> discard.late.arrival, <DOUBLE> error.threshold, <DOUBLE> confidence.level)
```

<span id="query-parameters" class="md-typeset" style="display: block; color: rgba(0, 0, 0, 0.54); font-size: 12.8px; font-weight: bold;">QUERY PARAMETERS</span>
<table>
    <tr>
        <th>Name</th>
        <th style="min-width: 20em">Description</th>
        <th>Default Value</th>
        <th>Possible Data Types</th>
        <th>Optional</th>
        <th>Dynamic</th>
    </tr>
    <tr>
        <td style="vertical-align: top">timestamp</td>
        <td style="vertical-align: top; word-wrap: break-word">The event timestamp on which the events should be ordered.</td>
        <td style="vertical-align: top"></td>
        <td style="vertical-align: top">LONG</td>
        <td style="vertical-align: top">No</td>
        <td style="vertical-align: top">Yes</td>
    </tr>
    <tr>
        <td style="vertical-align: top">correlation.field</td>
        <td style="vertical-align: top; word-wrap: break-word">By monitoring the changes in this field Alpha K-Slack dynamically optimises its behavior. This field is used to calculate the runtime window coverage threshold, which represents the upper limit set for unsuccessfully handled late arrivals.</td>
        <td style="vertical-align: top"></td>
        <td style="vertical-align: top">INT<br>FLOAT<br>LONG<br>DOUBLE</td>
        <td style="vertical-align: top">No</td>
        <td style="vertical-align: top">Yes</td>
    </tr>
    <tr>
        <td style="vertical-align: top">batch.size</td>
        <td style="vertical-align: top; word-wrap: break-word">The parameter 'batch.size' denotes the number of events that should be considered in the calculation of an alpha value. This should be greater than or equal to 15.</td>
        <td style="vertical-align: top">`10,000`</td>
        <td style="vertical-align: top">LONG</td>
        <td style="vertical-align: top">Yes</td>
        <td style="vertical-align: top">No</td>
    </tr>
    <tr>
        <td style="vertical-align: top">timeout</td>
        <td style="vertical-align: top; word-wrap: break-word">A timeout value in milliseconds, where the buffered events who are older than the given timeout period get flushed every second.</td>
        <td style="vertical-align: top">`-1` (timeout is infinite)</td>
        <td style="vertical-align: top">LONG</td>
        <td style="vertical-align: top">Yes</td>
        <td style="vertical-align: top">No</td>
    </tr>
    <tr>
        <td style="vertical-align: top">max.k</td>
        <td style="vertical-align: top; word-wrap: break-word">The maximum K-Slack window threshold ('K' parameter).</td>
        <td style="vertical-align: top">`9,223,372,036,854,775,807` (The maximum Long value)</td>
        <td style="vertical-align: top">LONG</td>
        <td style="vertical-align: top">Yes</td>
        <td style="vertical-align: top">No</td>
    </tr>
    <tr>
        <td style="vertical-align: top">discard.late.arrival</td>
        <td style="vertical-align: top; word-wrap: break-word">If set to <code>true</code> the processor would discarded the out-of-order events arriving later than the K-Slack window, and in otherwise it allows the late arrivals to proceed.</td>
        <td style="vertical-align: top">false</td>
        <td style="vertical-align: top">BOOL</td>
        <td style="vertical-align: top">Yes</td>
        <td style="vertical-align: top">No</td>
    </tr>
    <tr>
        <td style="vertical-align: top">error.threshold</td>
        <td style="vertical-align: top; word-wrap: break-word">The error threshold to be applied in Alpha K-Slack algorithm. </td>
        <td style="vertical-align: top">`0.03` (3%)</td>
        <td style="vertical-align: top">DOUBLE</td>
        <td style="vertical-align: top">Yes</td>
        <td style="vertical-align: top">No</td>
    </tr>
    <tr>
        <td style="vertical-align: top">confidence.level</td>
        <td style="vertical-align: top; word-wrap: break-word">The confidence level to be applied in Alpha K-Slack algorithm.</td>
        <td style="vertical-align: top">`0.95` (95%)</td>
        <td style="vertical-align: top">DOUBLE</td>
        <td style="vertical-align: top">Yes</td>
        <td style="vertical-align: top">No</td>
    </tr>
</table>

<span id="examples" class="md-typeset" style="display: block; font-weight: bold;">Examples</span>
<span id="example-1" class="md-typeset" style="display: block; color: rgba(0, 0, 0, 0.54); font-size: 12.8px; font-weight: bold;">EXAMPLE 1</span>
```
define stream StockStream (eventTime long, symbol string, volume long);

@info(name = 'query1')
from StockStream#reorder:akslack(eventTime, volume, 20)#window.time(5 min)
select eventTime, symbol, sum(volume) as total
insert into OutputStream;
```
<p style="word-wrap: break-word">The query reorders events based on the 'eventTime' attribute value and optimises for aggregating 'volume' attribute considering last 20 events.</p>

### kslack *<a target="_blank" href="http://siddhi.io/en/v5.0/docs/query-guide/#stream-processor">(Stream Processor)</a>*
<p style="word-wrap: break-word">Stream processor performs reordering of out-of-order events using [K-Slack algorithm](https://www2.informatik.uni-erlangen.de/publication/download/IPDPS2013.pdf).</p>
<span id="syntax" class="md-typeset" style="display: block; font-weight: bold;">Syntax</span>

```
reorder:kslack(<LONG> timestamp)
reorder:kslack(<LONG> timestamp, <LONG> timeout)
reorder:kslack(<LONG> timestamp, <BOOL> discard.late.arrival)
reorder:kslack(<LONG> timestamp, <LONG> timeout, <LONG> max.k)
reorder:kslack(<LONG> timestamp, <LONG> timeout, <BOOL> discard.late.arrival)
reorder:kslack(<LONG> timestamp, <LONG> timeout, <LONG> max.k, <BOOL> discard.late.arrival)
```

<span id="query-parameters" class="md-typeset" style="display: block; color: rgba(0, 0, 0, 0.54); font-size: 12.8px; font-weight: bold;">QUERY PARAMETERS</span>
<table>
    <tr>
        <th>Name</th>
        <th style="min-width: 20em">Description</th>
        <th>Default Value</th>
        <th>Possible Data Types</th>
        <th>Optional</th>
        <th>Dynamic</th>
    </tr>
    <tr>
        <td style="vertical-align: top">timestamp</td>
        <td style="vertical-align: top; word-wrap: break-word">The event timestamp on which the events should be ordered.</td>
        <td style="vertical-align: top"></td>
        <td style="vertical-align: top">LONG</td>
        <td style="vertical-align: top">No</td>
        <td style="vertical-align: top">Yes</td>
    </tr>
    <tr>
        <td style="vertical-align: top">timeout</td>
        <td style="vertical-align: top; word-wrap: break-word">A timeout value in milliseconds, where the buffered events who are older than the given timeout period get flushed every second.</td>
        <td style="vertical-align: top">`-1` (timeout is infinite)</td>
        <td style="vertical-align: top">LONG</td>
        <td style="vertical-align: top">Yes</td>
        <td style="vertical-align: top">No</td>
    </tr>
    <tr>
        <td style="vertical-align: top">max.k</td>
        <td style="vertical-align: top; word-wrap: break-word">The maximum K-Slack window threshold ('K' parameter).</td>
        <td style="vertical-align: top">`9,223,372,036,854,775,807` (The maximum Long value)</td>
        <td style="vertical-align: top">LONG</td>
        <td style="vertical-align: top">Yes</td>
        <td style="vertical-align: top">No</td>
    </tr>
    <tr>
        <td style="vertical-align: top">discard.late.arrival</td>
        <td style="vertical-align: top; word-wrap: break-word">If set to <code>true</code> the processor would discarded the out-of-order events arriving later than the K-Slack window, and in otherwise it allows the late arrivals to proceed.</td>
        <td style="vertical-align: top">false</td>
        <td style="vertical-align: top">BOOL</td>
        <td style="vertical-align: top">Yes</td>
        <td style="vertical-align: top">No</td>
    </tr>
</table>

<span id="examples" class="md-typeset" style="display: block; font-weight: bold;">Examples</span>
<span id="example-1" class="md-typeset" style="display: block; color: rgba(0, 0, 0, 0.54); font-size: 12.8px; font-weight: bold;">EXAMPLE 1</span>
```
define stream StockStream (eventTime long, symbol string, volume long);

@info(name = 'query1')
from StockStream#reorder:kslack(eventTime, 5000)
select eventTime, symbol, volume
insert into OutputStream;
```
<p style="word-wrap: break-word">The query reorders events based on the 'eventTime' attribute value, and it forcefully flushes all the events who have arrived older than the given 'timeout' value (<code>5000</code> milliseconds) every second.</p>

