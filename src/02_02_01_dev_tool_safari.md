# Developing in Safari

To use the devtool in safari you need to disable the local file restrictions
under `Develop` -> `Disable Local File Restrictions`:

<p>
<img
src="images/dev_tool_settings_safari.png"
alt="'Disable Local File Restrictions' entry in the 'Develop' menu in safari"
style="max-height:40vh"
/>
</p>

After doing this you can use the dev tool simulator.

Make sure to reload (`Cmd + R`) all simulator tabs/windows to apply this setting.

Without this option `Add Peer` seems to work (it opens a new instance), but **the instances will not be able to communicate**.
