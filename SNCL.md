# Geomag Channel Naming Proposal

> NOTE: this is based on an internal USGS proposal, so references to how
> things currently work refer to existing USGS implementation.

Background
Each time series stored in the edge is uniquely identified by four attributes:
Station, Channel, Network, and Location (frequently abbreviated SCNL, largely
because Station and Channel were the original identifiers and Network and later
Location were added as more instrumentation became available).

For geomag data, station is currently set to observatory code (e.g. `BOU` for
Boulder), network is currently set to `NT` for the USGS Geomagnetism Program,
location is currently set based on the data status (e.g. `R0` for internet,
`R1` for satellite, …).

Some channels are using a geomag specific convention, while newer channels are
following SEED conventions (because it is considered the correct way).  @jmfee-usgs
proposes @usgs not just adopt SEED conventions for new channels, but additionally
update all channel naming conventions to follow SEED standards.  This is
non-trivial in the short term, and likely requires populating both sets of
channels during transition.  However, the long term benefits are improved
interoperability with other Geomagnetism groups.

MiniSEED conventions are described:
- in a SEED Manual Appendix:

  http://www.fdsn.org/seed_manual/SEEDManual_V2.4_Appendix-A.pdf

- also as part of the full SEED manual (p133):

  http://www.fdsn.org/seed_manual/SEEDManual_V2.4.pdf


## Instruments

### Fluxgate Magnetometer

Triaxial analog magnetometer

- instrument code `F`
- orientation codes
  - `U` observatory/instrument north
  - `V` observatory/instrument east
  - `W` observatory/instrument down
- location codes
  - `R0` internet
  - `R1` GOES

Computed F

- instrument code `F`
- orientation codes `F`
- location codes
  - `RS` internet

### Overhauser Magnetometer

Scalar magnetometer

- instrument code `F`
- orientation codes `F`
- location codes
  - `R0` internet
  - `R1` GOES

### QuSpin Magnetometer

Scalar magnetometer

- instrument code `F`
- orientation codes `F`
- location codes
  - `RQ` internet

### Electrodes

Analog voltage measurements

- instrument code `Q`
- orientation codes
  - `X` geographic north
  - `Y` geographic east
  - `Z` (unlikely) geographic down
- location codes
  - `R0` 100 meter separation
  - `R1` 200 meter separation

### Induction Coil Magnetometer

Higher frequency magnetometer measurements

- instrument code `F`
- orientation codes
  - `X` geographic north
  - `Y` geographic east
  - `Z` geographic down
- location codes
  - `I0`

### Housekeeping Signals

Temperature

- instrument code `K`
- orientation code 1-5 (TODO: enumerate)
- location codes
  - `R0` internet

Battery Voltage
- instrument code `E`
- orientation code 1-2 (TODO: enumerate)
- location codes
  - `R0` internet


## Network Code

Network codes current use `NT`, which has been registered with FDSN as the USGS Geomagnetism Network.
Since @usgs also store data for other organizations observatories, it may be worth using separate network codes.


## Station Code

Station codes currently map to Observatory codes, for example `BOU` for Boulder.

Multiple acquisition systems at the same observatory currently use different Station codes,
rather than the more typical differing Location codes.


## Channel Code

The first character of a channel code normally defines frequency,
and SEED specifies characters for frequency ranges.

The second character of a channel code normally defines the instrument type.

The third character of a channel code normally differentiates between multiple
components from the same instrument.

### Proposal

#### First character

- `B` 10 Hz
- `L` 1 Hz (second)
- `U` ~0.01 Hz (minute)
- `R` (hour)
- `P` (day)

#### Second and Third characters

Below are remaining characters in the form:
> - second character
>   - third character

- `E` electronic test point (V)
  - `U` h (observatory/instrument north) volt
  - `V` e (observatory/instrument east) volt
  - `W` z (observatory/instrument down) volt
  - `F` f (observatory/instrument total field) volt

- `F` - magnetometer (T)
  - `U`/`V`/`W` observatory north/east/down
  - `H`/`D` magnetic horizontal/declination
  - `X`/`Y`/`Z` geographic north/east/down
  - `F` magnetic total field
  - `G` delta f (total field - |vector field|)
- `K` temperature (C or K)
  - `O` outside
  - `F` fluxgate
  - `E` electronics
- `Q` electric potential (V)
  - `U` e-field observatory/instrument north
  - `V` e-field observatory/instrument east
  - `X` e-field geographic north
  - `Y` e-field geographic east
- `X` derived or generated
  - `A` ae
  - `K` k index
  - `3` DST 3 channel
  - `4` DST 4 channel
- `Y` non-specific instrument
  - `U` h (observatory/instrument north) bin
  - `V` e (observatory/instrument east) bin
  - `W` z (observatory/instrument down) bin
  - `F` f (observatory/instrument total field) bin

#### Examples
1 minute observatory H: UFU
1 minute observatory E: UFV
1 minute observatory Z: UFW
1 minute observatory F: UFF
1 minute magnetic H: UFH
1 minute magnetic D: UFD

## Location Code

Location codes/identifiers are 2-character strings that can be used to specify
details beyond channel information.  Location codes are typically used to identify
multiple instruments of the same type within a single station, that would otherwise
produce the same channel codes.

Geomag currently uses the first character to indicate the source and level of processing
for the data, e.g. `R0` is raw-internet, `R1` is raw-satellite, `A0` is adjusted, etc.

### Proposal

#### First character
- `R` Raw
- `A` Adjusted
- `Q` Quasi-definitive
- `D` Definitive

#### Second character
- `0` no additional processing within data type (`A0` is the most basic adjusted).
- `D` Disturbance for channel, from `_0` location code
- `Q` Solar Quiet for channel, from `_0` location code
- `V` Secular Variation for channel, from `_0` location code
- `T` Time derivative for channel, from `_0` location code
  



## Examples

These examples are combinations of channel and location codes (5 characters total).

These are the 10Hz channels to be transmitted from each observatory:
- `BEUR0` 10Hz observatory H voltage
- `BYUR0` 10Hz observatory H bin

- `BEVR0` 10Hz observatory E voltage
- `BYVR0` 10Hz observatory E bin

- `BEWR0` 10Hz observatory Z voltage
- `BYWR0` 10Hz observatory Z bin

- `BEFR0` 10Hz observatory F voltage
- `BYFR0` 10Hz observatory F bin

Plus housekeeping channels like temperature

From the above channels, the following engineering-unit channels can be generated:

- `BFUR0` 10Hz observatory H pT
- `BFVR0` 10Hz observatory E pT
- `BFWR0` 10Hz observatory Z pT
- `BFFR0` 10Hz observatory F pT

- `BFHR0` 10Hz magnetic H pT
- `BFDR0` 10Hz magnetic D 10*milli-arcminutes

- `BFXR0` 10Hz geographic north pT
- `BFYR0` 10Hz geographic east pT
- `BFZR0` 10Hz geographic down pT

From engineering-unit channels, additional derived channels are generated:

- `BFGR0` 10Hz deltaF pT (from observatory components)

- `LFUR0` 1Hz observatory H pT
- `LFVR0` 1Hz observatory E pT
- `LFWR0` 1Hz observatory Z pT
- `LFFR0` 1Hz observatory F pT

- `LFHR0` 1Hz magnetic H pT
- `LFDR0` 1Hz magnetic D

- `LFXR0` 1Hz geographic north pT
- `LFYR0` 1Hz geographic east pT

From one-second channels, additional derived channels are generated:
- `LFGR0` 1Hz deltaF pT (from observatory 1Hz components)

- `LFURD` 1Hz observatory H Disturbance pT
- `LFURQ` 1Hz observatory H Solar Quiet pT
- `LFURV` 1Hz observatory H Solar Variation pT

And equivalent Sq/dist channels for D,X,Y,Z

- `UFUR0` 1-minute observatory H pT
- `UFVR0` 1-minute observatory E pT
- `UFWR0` 1-minute observatory Z pT
- `UFFR0` 1-minute observatory F pT

- `UFHR0` 1-minute magnetic H pT
- `UFDR0` 1-minute magnetic D

- `UFXR0` 1-minute geographic north pT
- `UFYR0` 1-minute geographic east pT
- `UFZR0` 1-minute geographic down pT

Derived indexes within the `NT` network `USGS` "station"
- `UX3R0` 3 channel USGS Dist
- `UX4R0` 4 channel Dist
- `UXKR0` k index
