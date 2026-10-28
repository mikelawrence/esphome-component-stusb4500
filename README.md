# ESPHome Component STUSB4500

The STUSB4500 is a USB power delivery controller that supports sink up to 100 W (20V, 5A). It has Non-Volatile Memory that can be programmed with your PDO profile so when you connect to a USB-C Power Source it will immediately negotiate your power delivery contract.

<p align="center">
    <img src="https://www.sparkfun.com/media/catalog/product/cache/a793f13fd3d678cea13d28206895ba0c/1/5/15801-SparkFun_Power_Delivery_Board_-_USB-C__Qwiic_-04.jpg" width="30%"><br />
    STUSB4500 board from SparkFun Electronics
</p>

There are 3 PDOs that you can configure with PD1 fixed at 5V. You can vary PD1's current. If you have all three PDOs configured the STUSB4500 will try to negotiate a contract with the PDOs in the following order: PD3, PD2 and PD1. If you don't want PDO1 negotiated then set `power_only_above_5v: true` .

The configuration below the STUSB4500 will try to negotiate 12V @ 3A then 9V @ 3A. If neither are available then it won't negotiate at all.

It also creates a `PD State` and `PD Status` Text Sensor.

```yaml
# Sample configuration entry example
external_components:
  - source:
      type: git
      url: https://github.com/mikelawrence/esphome-components
    components: [ stusb4500 ]

stusb4500:
  alert_pin: GPIO8
  snk_pdo_numb: 3
  v_snk_pdo2: 9.00V
  i_snk_pdo2: 3.00A
  v_snk_pdo3: 12.00V
  i_snk_pdo3: 3.00A
  power_only_above_5v: true
  usb_comms_capable: true

sensor:
  - platform: stusb4500
    pd_state:
      name: "PD State"

text_sensor:
  - platform: stusb4500
    pd_status:
      name: "PD Status"
```

## Configuration Variables

* **flash_nvm** (*Optional*, boolean): When set to `true` the STUSB4500 NVM will be flashed with the current settings but only if different. To be on the safe side you should not leave this set to `true` . A power cycle is required to renegotiate. Default is `false` .
* **default_nvm** (*Optional*, boolean): When set to `true` the STUSB4500 NVM will be flashed with default settings but only if different. To be on the safe side you should not leave this set to `true` . A power cycle is required to renegotiate. Default is `false` .
* **alert_pin** (*Optional*, pin): GPIO connected to the STUSB4500 alert output.
* **snk_pdo_numb** (*Optional*, integer): Set the number of PDOs that should be negotiated. Range is 1 - 3. A value of 3 means PDO3, PDO2 and PDO1 can be negotiated. A value of 2 means PDO2 and PDO1 can be negotiated. A value of 1 means only PDO1 can be negotiated. Default is 3.
* **v_snk_pdo2** (*Optional*, float): This is the desired PDO2 voltage. Range is 5.0 to 20.0. Default is 20.0.
* **v_snk_pdo3** (*Optional*, float): This is the desired PDO3 voltage. Range is 5.0 to 20.0. Default is 15.0.
* **i_snk_pdo1** (*Optional*, float): This is the desired PDO1 current. Range is 0.0 to 5.0. 5.0A is only available with 20V. For all other voltages the maximum current is 3.0A. Default is 1.5.
* **i_snk_pdo2** (*Optional*, float): This is the desired PDO2 current. Range is 0.0 to 5.0. 5.0A is only available with 20V. For all other voltages the maximum current is 3.0A. Default is 1.5.
* **i_snk_pdo3** (*Optional*, float): This is the desired PDO3 current. Range is 0.0 to 5.0. 5.0A is only available with 20V. For all other voltages the maximum current is 3.0A. Default is 1.0.
* **i_snk_pdo_flex** (*Optional*, float): Current for a flexible PDO, from 0.01A to 5.0A in steps of 0.01A.
* **shift_vbus_hl1** (*Optional*, percentage): This is the percentage above PDO1 voltage when the Over Voltage Lock Out occurs. Range is 1% to 15%. Default is 10%.
* **shift_vbus_ll1** (*Optional*, percentage): This is the percentage below PDO1 voltage when the Under Voltage Lock Out occurs. Range is 1% to 15%. Default is 15%.
* **shift_vbus_hl2** (*Optional*, percentage): This is the percentage above PDO2 voltage when the Over Voltage Lock Out occurs. Range is 1% to 15%. Default is 5%.
* **shift_vbus_ll2** (*Optional*, percentage): This is the percentage below PDO2 voltage when the Under Voltage Lock Out occurs. Range is 1% to 15%. Default is 15%.
* **shift_vbus_hl3** (*Optional*, percentage): This is the percentage above PDO3 voltage when the Over Voltage Lock Out occurs. Range is 1% to 15%. Default is 5%.
* **shift_vbus_ll3** (*Optional*, percentage): This is the percentage below PDO3 voltage when the Under Voltage Lock Out occurs. Range is 1% to 15%. Default is 15%.
* **vbus_disch_time_to_0v** (*Optional*, time): Coefficient used to compute V<sub>BUS</sub> discharge time to 0V. Range is 84ms to 1260ms and must be a multiple of 84ms. Default is 756ms.
* **vbus_disch_time_to_pdo** (*Optional*, time): Coefficient used to compute V<sub>BUS</sub> discharge time when transitioning to lower PDO voltage. Range is 24ms to 360ms and must be a multiple of 24ms. Default is 288ms.
* **vbus_disch_disable** (*Optional*, boolean): When set to `true` the STUSB4500 NVM will stop discharging V<sub>BUS</sub>. Default is `false` .
* **usb_comms_capable** (*Optional*, boolean): When set to `true` the STUSB4500 will tell the connected device that USB communications are possible. Default is `false` .
* **snk_uncons_power** (*Optional*, boolean): When set to `true` the STUSB4500 will tell the connected device external power is available. Default is `false` .
* **req_src_current** (*Optional*, boolean): When set to `true` the STUSB4500 will report the source current from source instead of the sink. Default is `false` .
* **power_ok_cfg** (*Optional*, enumeration): Selects the POWER_OK pins configuration. Options are `CONFIGURATION_1` , `CONFIGURATION_2` or `CONFIGURATION_3` . Default is `CONFIGURATION_2` .
* **gpio_cfg** (*Optional*, enumeration): Selects the GPIO configuration. Options are `SW_CTRL_GPIO` , `ERROR_RECOVERY` , `DEBUG` or `SINK_POWER` . Default is `ERROR_RECOVERY` .
* **power_only_above_5v** (*Optional*, boolean): When set to `true` the STUSB4500 will enable V<sub>BUS</sub> only if PDO2 or PDO3 was negotiated. When `false` V<sub>BUS</sub> is enabled at 5V when connected to a non-PD charger. The available current is unknown. Default is `false` .

## Sensors

* **pd_state** (*Optional*): Current PD State as an integer. Range 0 to 3 where 0 is no PD negotiated. A value of 1, 2 or 3 indicates which PDO was negotiated. All Options from [Sensor](https://esphome.io/components/sensor/#config-sensor).

## Text Sensors

* **pd_status** (*Optional*): Easy to read PD State (e.g. "12V @ 3A" ). All Options from [Sensor](https://esphome.io/components/sensor/#config-sensor).

## Actions

When the GPIO is configured for software control `gpio_cfg: SW_CTRL_GPIO` these actions control the state of the output.

* **stusb4500.turn_gpio_on** Will pull the GPIO low.
* **stusb4500.turn_gpio_off** Will set the output to High-Z.

Example using automations...

```yaml
on_value_range:
  - below: 5.0
    then:
      - stusb4500.turn_gpio_on: pd_controller
```

Example in lambdas...

```yaml
- lambda: |-
  id(pd_controller).turn_gpio_on();
```

## NVM Configuration

> [!CAUTION]
> When configuring the STUSB4500 for the first time you should realize the default configuration will allow up to 20V on V<sub>BUS</sub>. Make sure your circuit can either support 20V or configure the NVM using a Power Source that will NOT produce 20V like USB 2.0 or a non-PD USB-C charger.

Using the STUSB4500 ESPHome component requires the following steps:

1. Set the configuration variables to match your requirements. The sample configuration above sets PDO3 to 12V @ 3A, PDO2 to 9V @3A and PDO1 is disabled because `power_only_above_5v: true` . This means it will first try to negotiate 12V @ 3A. If unavailable then it will try 9V @ 3A. If is unavailable no PD will be negotiated. Either the `pd_state` or `pd_status` sensor will let you know if there is a problem.
2. Add `flash_nvm: true` to the configuration.
   You should see the following messages in your log.

     ```log
     [00:10:03][C][stusb4500:112]: STUSB4500:
     [00:10:03][E][stusb4500:119]:   NVM has been flashed, power cycle the device to reload NVM
     `

   If you forgot to add `flash_nvm: true` to the configuration you will see an error in the log.

     ```log
     [00:06:27][C][stusb4500:112]: STUSB4500:
     [00:06:27][E][stusb4500:114]:   NVM does not match current settings, you must set flash_nvm: true
     [00:06:27][C][stusb4500:130]:   PDO3 negotiated 9.00V @ 3.00A, 27.00W
     ```

3. Power cycle your board. Make sure `NVM matches settings` is present in the log and that one of your PDOs was negotiated. Configuring the STUSB4500 can be a bit finicky, but keep at it.

   ```log
   [00:00:19][C][stusb4500:112]: STUSB4500:
   [00:00:19][C][stusb4500:116]:   NVM matches settings
   [00:00:19][C][stusb4500:130]:   PDO3 negotiated 12.00V @ 3.00A, 36.00W
   ```

4. The STUSB4500 component does check that the NVM is indeed different before flashing it but it is prudent to remove the `flash_nvm: true` after it is clear the STUSB4500 is working as configured.

## Development with VS Code Dev Containers

Open this component directory as the VS Code workspace and reopen it in the Dev Container. All component checkouts share the same persistent ESPHome, PlatformIO, and pip cache volumes; Docker also reuses locally downloaded ESPHome images.

Run the VS Code task **Clear cached downloads** to empty the shared cache volumes and remove locally downloaded `ghcr.io/esphome/esphome` images. Close the Dev Container before running the task so Docker can remove images that are in use.

On Windows, the VS Code **Terminal > Run Build Task** command runs the default **ML ESPHome: Build ESPHome test configs** task. It runs **ML ESPHome: Validate ESPHome component** first, and only compiles if validation succeeds. Validation applies supported automatic fixes, then runs strict YAML linting, C/C++ formatting checks, Ruff format/lint, flake8, pylint, pyupgrade, and ESPHome-style text convention checks. Compilation covers every `.yaml` and `.yml` file under `test/` with the ESPHome `stable`, `beta`, and `dev` images.

Tool versions are pinned to the versions used by ESPHome CI, and the existing `.yamllint`, `.clang-format`, `.flake8`, and `pyproject.toml` configurations are used. clang-tidy is not included because it requires ESPHome's PlatformIO compilation metadata, which is not present in a standalone component checkout.

The build stage detects the GitHub owner/repository from the checkout's `origin` remote and replaces matching ESPHome GitHub component sources with the local `components/` directory in temporary copies of the test files; original test YAML files are not modified. Pass `-ComponentSource owner/repository` to `.devcontainer\build.ps1` if the checkout has no GitHub `origin`; use `-Versions @("dev")` to select ESPHome tags, or `-Image ghcr.io/esphome/esphome:<tag>` to use one custom image.

Run `.devcontainer\validate.ps1` alone to apply fixes and run validation without compiling. `yamllint`, flake8, pylint, and semantic or unsupported lint findings are not automatically rewritten and will be reported for manual attention. There is no separate unit-test runner; compiling the test configs is the integration/build test.
