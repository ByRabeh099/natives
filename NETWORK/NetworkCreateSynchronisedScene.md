---
ns: NETWORK
---
## NETWORK_CREATE_SYNCHRONISED_SCENE

```c
// 0x7CD6BC4C2BBDD526 0xB06FE3FE
int NETWORK_CREATE_SYNCHRONISED_SCENE(float x, float y, float z, float xRot, float yRot, float zRot, int rotationOrder, BOOL holdLastFrame, BOOL looped, float p9, float animTime, float animSpeed);
```
Creates a synchronised scene for the network.

## Parameters
* **x**: 
* **y**: 
* **z**: 
* **xRot**: 
* **yRot**: 
* **zRot**: 
* **rotationOrder**: 
* **holdLastFrame**: 
* **looped**: 
* **p9**: 
* **animTime**: 
* **animSpeed**: 

## Return value
The network scene ID or scene handle

## Examples
```lua
    local ped = PlayerPedId()
    local animDict = 'anim@gangops@facility@servers@bodysearch@'
    local sceneObject = GetClosestObjectOfType(coords, 6.0, GetHashKey('h4_prop_h4_chest_01a'), false, false, false)

    RequestAnimDict(animDict)
    while not HasAnimDictLoaded(animDict) do
      Wait(0)
    end

    local netScene = NetworkCreateSynchronisedScene(GetEntityCoords(sceneObject), GetEntityRotation(sceneObject), 2, true, false, 1, 15.666667, 1.0)
    NetworkAddPedToSynchronisedScene(ped, netScene, animDict, 'player_search', 1.0, 1.0, 15.666667, 17957384, 0)

    NetworkStartSynchronisedScene(netScene)
    -- 15.666667 Is the duration of the anim so we wait to the anim finish and stop the synchronised scene
    Wait(15.666667)
    ClearPedTasks(ped) -- You can also use NetworkStopSynchronisedScene()
    
```
## Advanced network scene:
```lua

    local ped = PlayerPedId()
    local pedCo = GetEntityCoords(playerPed)

    local animDict = 'anim@heists@ornate_bank@grab_cash'
    local bagModel = `hei_p_m_bag_var22_arm_s` -- We write ` instead of ' to directly get the hash of the model

    while not HasAnimDictLoaded(animDict) do Wait(0) end -- Reduce it to 1 line to prevent more unnecesary extra short lines

    while not HasModelLoaded(bagModel) do Wait(0) end -- Here the same

    local bag = CreateObject(bagModel, pedCo, true, true, false) -- We create the object bag on the ped coords to start giving it animations
    local sceneObject = GetClosestObjectOfType(coords, 6.0, GetHashKey(''), false, false, false)

    local netScenes = {}
    local animPhases = {  -- These are the animations we're going to use (grab ingots from a table). You can see the preview at:
    -- https://forge.plebmasters.de/animations/anim@scripted@heist@ig1_table_grab@cash@male@@enter?search=anim%40scripted%40heist%40ig1_table_grab%40cash%40male%40&ped=MP_M_Freemode_01
        { 'intro', 'bag_intro' },
        { 'grab', 'bag_grab' },
        { 'exit', 'bag_exit' }
    }

  for i=1, #animPhases do
    netScenes[i] = NetworkCreateSynchronisedScene(GetEntityCoords(sceneObject), GetEntityRotation(sceneObject), 2, true, false, 1065353216, 0, 1.3) -- Here the anim duration is too big!
    NetworkAddPedToSynchronisedScene(PlayerPedId(), cutScenes[i], animDict, animPhases[i][1], 1.5, -4.0, 1, 16, 1148846080, 0)
    NetworkAddEntityToSynchronisedScene(bag, cutScenes[i], animDict, animPhases[i][2], 4.0, -8.0, 1)
    if i == 2 then
        NetworkAddEntityToSynchronisedScene(sceneObject, cutScenes[i], animDict, "cart_cash_dissapear", 4.0, -8.0, 1)
    end
  end
  -- Thanks to HardDevStyles for these waits
  NetworkStartSynchronisedScene(cutScenes[1])
  Wait(1750)
  handItem(handItemModel)
  NetworkStartSynchronisedScene(cutScenes[2])
  Wait(37 * 1000)
  NetworkStartSynchronisedScene(cutScenes[3])
  Wait(2000)

  DeleteObjects(bag)
  ClearPedTasks(ped)

  -- We've finally finished! We've executed a synchronised scene with the length of animPhases (3), and then for every iteration we gave a different animation to the bag and player,
  -- after having it created we've started it with different waits (the anim duration between synchronised scenes) then we delete the bag and we clear de the ped tasks.
```
