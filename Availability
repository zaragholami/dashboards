<form version="1.1" theme="dark">
  <label>Enterprise Host Availability Monitor</label>
  <description>Advanced monitoring dashboard for detecting hosts that stopped sending logs</description>
  <fieldset submitButton="false">
    <input type="time" token="time_range">
      <label>Time Range</label>
      <default>
        <earliest>-24h</earliest>
        <latest>now</latest>
      </default>
    </input>
    <input type="text" token="index_filter">
      <label>Index</label>
      <default>*</default>
    </input>
    <input type="text" token="sourcetype_filter">
      <label>Sourcetype</label>
      <default>*</default>
    </input>
    <input type="dropdown" token="down_threshold">
      <label>Down Threshold (minutes)</label>
      <choice value="15">15</choice>
      <choice value="30">30</choice>
      <choice value="60">60</choice>
      <default>30</default>
    </input>
  </fieldset>
  <row>
    <panel>
      <title>Host Status Summary</title>
      <chart>
        <search>
          <query>| tstats latest(_time) as last_event where index=$index_filter$ sourcetype=$sourcetype_filter$ by host
| eval minutes_down=round((now()-last_event)/60,1)
| eval status=if(minutes_down&gt;$down_threshold$,"DOWN","UP")
| stats count by status</query>
          <earliest>$time_range.earliest$</earliest>
          <latest>$time_range.latest$</latest>
        </search>
        <option name="charting.chart">pie</option>
        <option name="charting.legend.placement">right</option>
        <option name="refresh.display">progressbar</option>
      </chart>
    </panel>
    <panel>
      <title>Total Hosts Reporting</title>
      <single>
        <search>
          <query>| tstats dc(host) as total_hosts where index=$index_filter$</query>
          <earliest>$time_range.earliest$</earliest>
          <latest>$time_range.latest$</latest>
        </search>
        <option name="drilldown">all</option>
        <option name="refresh.display">progressbar</option>
      </single>
    </panel>
    <panel>
      <title>Hosts Currently Down</title>
      <single>
        <search>
          <query>| tstats latest(_time) as last_event where index=$index_filter$ by host
| eval minutes_down=(now()-last_event)/60
| where minutes_down&gt;$down_threshold$
| stats count as down_hosts</query>
          <earliest>$time_range.earliest$</earliest>
          <latest>$time_range.latest$</latest>
        </search>
        <option name="drilldown">all</option>
        <option name="refresh.display">progressbar</option>
      </single>
    </panel>
  </row>
  <row>
    <panel>
      <title>Hosts Currently Down</title>
      <table>
        <search>
          <query>| tstats latest(_time) as last_event where index=$index_filter$ sourcetype=$sourcetype_filter$ by host
| eval minutes_down=round((now()-last_event)/60,1)
| eval hours_down=round(minutes_down/60,2)
| where minutes_down&gt;$down_threshold$
| eval last_seen=strftime(last_event,"%Y-%m-%d %H:%M:%S")
| table host last_seen minutes_down hours_down
| sort -minutes_down</query>
          <earliest>$time_range.earliest$</earliest>
          <latest>$time_range.latest$</latest>
        </search>
        <option name="refresh.display">progressbar</option>
        <drilldown>
          <set token="selected_host">$row.host$</set>
        </drilldown>
      </table>
    </panel>
  </row>
  <row>
    <panel>
      <title>Top Hosts by Downtime</title>
      <chart>
        <search>
          <query>| tstats latest(_time) as last_event where index=$index_filter$ by host
| eval minutes_down=round((now()-last_event)/60,1)
| where minutes_down&gt;$down_threshold$
| sort -minutes_down
| head 10
| table host minutes_down</query>
          <earliest>$time_range.earliest$</earliest>
          <latest>$time_range.latest$</latest>
        </search>
        <option name="charting.chart">bar</option>
        <option name="refresh.display">progressbar</option>
      </chart>
    </panel>
    <panel>
      <title>Active Hosts Trend</title>
      <chart>
        <search>
          <query>index=$index_filter$ earliest=-24h
| timechart span=30m dc(host) as active_hosts</query>
          <earliest>$time_range.earliest$</earliest>
          <latest>$time_range.latest$</latest>
        </search>
        <option name="charting.chart">line</option>
        <option name="refresh.display">progressbar</option>
      </chart>
    </panel>
  </row>
  <row>
    <panel>
      <title>Recently Stopped Hosts</title>
      <table>
        <search>
          <query>| tstats latest(_time) as last_event where index=$index_filter$ by host
| eval minutes_down=round((now()-last_event)/60,1)
| where minutes_down&gt;$down_threshold$ AND minutes_down&lt;120
| eval last_seen=strftime(last_event,"%Y-%m-%d %H:%M:%S")
| table host last_seen minutes_down
| sort -minutes_down</query>
          <earliest>$time_range.earliest$</earliest>
          <latest>$time_range.latest$</latest>
        </search>
        <option name="refresh.display">progressbar</option>
      </table>
    </panel>
  </row>
  <row depends="$selected_host$">
    <panel>
      <title>Host Event Details - $selected_host$</title>
      <table>
        <search>
          <query>index=$index_filter$ host=$selected_host$
| sort -_time
| head 100</query>
          <earliest>$time_range.earliest$</earliest>
          <latest>$time_range.latest$</latest>
        </search>
        <option name="refresh.display">progressbar</option>
      </table>
    </panel>
  </row>
</form>
