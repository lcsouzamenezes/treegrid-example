# ARIA treegrid example

[Jump to interactive version](https://cdn.rawgit.com/aleventhal/treegrid-example/0.1.15/treegrid.html)

# Rationale

The ARIA treegrid has not gotten enough love. Docs the old specs provide recommendations for keyboard shortcuts that would conflict with browser/OS keys.

# Challenges

The main challenge is that the left/right arrow key could be used to collapse/expand or to move by column. The few approaches out there take the approach that left/right should move by cell, but this takes the opposite approach. In this example, left/right collapse/expand, and move-by-word keys are used to move by cell.

# Assumptions

* Most treegrids don't have that many columns, and are more vertical than horizontal
* Most users know how to navigate a tree
* The most important navigation in a treegrid is by row, like in a normal tree
* Navigation by cell is often more of a convenience than a necessity

# Approach
* By default, a user is in row navigation mode. We allow traversal of columns via the move-by-word keys.
* Ideally, screen readers would automatically read from the currently focused cell to the end of the row. The benefit of this would be that the user could effectively always be in row mode, as the screen reader would end up reading the entire line when moving to the next row anyway. However, screen readers don't necessarily do this currently. Thus, the approach was taken that when the first column would have been active, we focus the entire row, to help the screen reader know to read the entire thing.

# Keyboard specifics
* Up/down - moves by row is in an ordinary tree
* Left/right - collapses/expands or moves to parent/first-child as in an ordinary tree. TBD, what should these keys do during column navigation?
* Move by word key (ctrl+left/right or alt/left+right depending on the platform) - move by column
* Home/end - move to first/last row or column, depending on whether the last thing focused was a row or column

# Markup used
* roles used are treegrid, row and gridcell
* aria-level: used to set the current level of an item, 1, 2, 3, etc.
* aria-posinset, aria-setsize: used to indicate the position of an item within it's local group, such as item 3 of 5. TBD, should we set these on cells as well so that the user knows what column # there are in? Possibly overkill.
* aria-expanded (triststate): this attribute must be removed (not present) if the item cannot be expanded. For expandable items, the value is "true" or "false"
* aria-hidden: set to "true" for child items that are currently hidden because the parent is collapsed
* aria-owns: not currently used, awaiting discussion. Could be used for parents to identify their children.
* aria-labelledby or aria-describedby for headers? Not currently used, awaiting discussion
* aria-activedescendant -- this example does not use, because it is not exposed to ATs in IE, and thus uses tabindex instead
* tabindex is set in the JS, as per the usual roving tabindex methodology. Specifically, we use tabindex="0" for the current item so that it is the subitem that gets focused if user tabs out and back in, and tabindex="-1" for all items where we want click-to-focus behavior enabled.

# Observations with screen readers

<table>
<thead>
  <tr>
    <th></th>
    <th>Chrome</th>
    <th>Firefox</th>
    <th>IE11</th>
    <th>Safari</th>
  </tr>
</thead>
<tbody>
<tr>
<th>Chomevox</th>
<td>Pretty broken: reads the words row, column, expanded but just about nothing else</td>
<td></td>
<td></td>
<td></td>
</tr>
<tr>
<th>JAWS</th>
<td>Pretty broken: reads the word expanded, but just about nothing else
<td>
  <li>Row navigation: levels not reported
  <li>Column navigation: no issues found
  <li>Other: when collapsing/expanding: new collapsed/expanded state not spoken, says "row unselected"
</td>
<td>Could not figure out how to interact with the control in a reasonable manner</td>
<td></td>
</tr>
<tr>
<th>NVDA</th>
<td>Pretty broken: reads the words row, column but just about nothing else</td>
<td>
  <li>Row navigation: levels read correctly, all seems good
  <li>Column navigation: cells are announced as "selected, editable" but aren't either
<td>
  <li>Column navigation: unlike in Firefox, NVDA does not read column labels but instead reads the column number
  <li>Row navigation: row numbers are reported, unlike with NVDA and Firefox. Unfortunately both row 2 and 3 are reported as
</tr>
<tr>
<th>VoiceOver</th>
<td>
  <li>First focus: calls it a listbox
  <li>Column navigation: reads each item twice
  <li>Row navigation: does not read expanded state, completely skips reading second column for second row
<td></td>
<td></td>
<td>Row navigation: doesn't let us do this, reads cells no matter what</td>
</tr>
</tbody>
</table>
