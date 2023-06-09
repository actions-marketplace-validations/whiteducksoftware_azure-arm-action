# Advanced example on how to use this Action
In this exmaple we deploy 2 templates (for the sake of the example the same template) but the second one depends on the first one as we need first the output of first one and second we need to override a parameter in the second template.   
Our template has two outputs `location` and `containerName`. But we are only interested in `containerName`, the first template will output that one and the second one requires that and appends `-overriden` so we can see it got overriden.

## Steps
```yaml
- uses: whiteducksoftware/azure-arm-action@master
  id: deploy
  with:
      creds: ${{ secrets.AZURE_CREDENTIALS }}
      resourceGroupName: azurearmaction
      templateLocation: examples/template/template.json
      parameters: examples/template/parameters.json
      deploymentName: github-advanced-test
```
Here we see a normal use of the Action, we pass the template as json file as well as the parameters. If we look into the `template.json` File we can see at the very bottom the defined outputs:
```json
{
  ..
  "outputs": {
    ...
    "containerName": {
      "type": "string",
      "value": "[parameters('containerName')]"
    }
  }
}
```
And we know our Action writes this output(s) to an action output variable with the same name, we can access it using `${{ steps.deploy.outputs.containerName }}` (Note: `deploy` comes from the `id` field from above.)   

If we now add a Shell script with a simple echo from that value,
```yaml
- run: echo ${{ steps.deploy.outputs.containerName }}
```
we can see that on the console will be `github-action` printed.

Now we add our second deployment which relies on that value and modfies the `containerName` parameter,
```yaml
- uses: whiteducksoftware/azure-arm-action@master
  id: deploy2
  with:
      creds: ${{ secrets.AZURE_CREDENTIALS }}
      resourceGroupName: azurearmaction
      templateLocation: examples/template/template.json
      parameters: examples/template/parameters.json
      deploymentName: github-advanced-test
      overrideParameters: |
        containerName=${{ steps.deploy.outputs.containerName }}-overriden
```
Look at the `overrideParameters` section, where we either could plug in another `parameter.json` File or we do it like here with line seperated key-value pairs. If we now add again a shell script to see our ouput,
```yaml
- run: echo ${{ steps.deploy2.outputs.containerName }}
```
we can see that on the console will be `github-action-overriden` printed.