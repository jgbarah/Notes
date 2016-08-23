# My life with Kibana & ElasticSearch

## Building a filter for a Kibana4 dashboard

Wnen the field to filter is already in some of the visualizations of the dashboard, that's rather easy: just click on the corresponding value in any of such visualizations.

But when the field is not in any visualization, other tricks may be used. For example:

* Go to the index in Discover (Discover in the top Kibana menu, then select the index on the top left, just below the search box)
* Open any of the records that appear (each corresponds to a JSON document in the index). You open them by clicking on the small triangle that apperars on the left of each record. That will unfold all fields in the record. Now, look for the field to use for the filter, and click on the "+" or "-" icon, depending on whether you want an excluding or including filter. That will produce a red (excluding) or green (including) filter box in the top (just below the search box).
* Edit the record, if needed (usually it is needed to set a name for the filter). Fro that, click on the "edit" icon in the filter itself. For setting a name for the filter, edit the "Filter alias" box.
* Now, make the filter sticky, by clicking on the pin icon, on the filter itself. This will make the filter stay even if you switch to a new dashboard in the same Kibana instance.
* With the filter made sticky, open the dashboard in which the filter is to be inserted. The filter will be there (it was sticky, after all, for a reason).
* Check that the filter is working, probably make the filter non-sticky (click on the pin icon again), and save the dashboard. The saved dashboard will include the filter.
