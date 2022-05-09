# Cribl Pack for Imperva WAF & DAM
----

Cribl Pack for Imperva WAF & DAM transforms events from Imperva Incapsula Web Application Firewalls & Database Activity Monitor to take less space in a SIEM and be searchable faster. Reduction results against sample data:
- DAM events with heavily searched fields sent as index-time: 49% reduction
- DAM events with all fields sent inside _raw: 43% reduction
- WAF events with heavily searched fields sent as index-time: 42% reduction
- WAF events with all fields sent inside _raw: 21% reduction

When index-timing fields and using the tstats command in Splunk, search performance can be 100-1000X better. Users are encouraged to explore what fields users search on the most and convert those fields to index time in Splunk. Users are also encouraged to see what other fields should be removed, as there can be lots of noise in these events.

For a destinations like Elastic, the pipeline should be slightly modified to have all fields outside _raw. Otherwise, they will be labelled as _raw.xxx for the field names in Elastic. This can be achieved by disabling some or all serialize functions.

The events are transformed in the following ways:
1. Create Key value pairs from the CEF format at the beginning of the events. Note that the WAF sample events in this pack differ slightly from the DAM sample events.
2. Transform many of the fields into index-time fields, rather than being in _raw 
3. Reducing _raw
4. Reformatting this data format: 
```cs4=103.6.32.100 cs4Label=clientIPAddress```
to this format:
```cs_clientIPAddress=103.6.32.100```

5. For Incapsula WAF events, there is the option to remove the 'postbody' field, which can save 20% on it's own

## Installation
---
1. Get the bits.
   1. Install the pack natively in Cribl Stream 3.4+
   2. Download the most recent .crbl file from the repo [releases page](https://github.com/criblpacks/cribl-imperva/releases).
   3. -or- Install in LogStream via the Github URL for this pack: `https://github.com/criblpacks/cribl-imperva.git`
2. Create a Route with a filter for your Imperva events.
3. Select the `Imperva Pack` pack as the pipeline.
4. Capture data on the wire to ensure your event formats match those in the pack. There may be some suttle differences, as Imperva allows you to change the event content.
5. If you prefer to have your fields named as such: ```clientIPAddress=103.6.32.100``` instead of ```cs.clientIPAddress=103.6.32.100``` then enable the rename function in each pipeline. You will also need to update the subsequent functions to reference the new fields.

## DAM and WAF Pipeline Configuration
---
1. In both pipelines, enable either Group B or Group C, NOT both. 
    Group B moves many heavily searched fields to index time.
    Group C keeps the index time fields to a minimum.
2. If you want to modify the index time fields in Group B, modify both functions to include/exclude fields. 

## Additional Optional Configuration for WAF Pipeline
1. Function #8 is an eval function that removes the 'postbody' field from the event. 
    If want this field, DISABLE this function
    If don't need this field, keep the function ENABLED
    This field can add 20-30% to event size

## Release Notes
---
### Version 0.3.0 
Initial release

## Contributing to the Pack
---
Discuss this pack on our Community Slack channel [#packs](https://cribl-community.slack.com/archives/C021UP7ETM3).

## Contact
---
The author of this pack is Ahmed Kira and can be contacted at <akira@cribl.io>. 
Special callout to Brendan Dalpe who crafted the initial version of the regex to extract all the cs fields. 

## License
---
This Pack uses the following license: [`Apache 2.0`](https://github.com/criblio/appscope/blob/master/LICENSE).
