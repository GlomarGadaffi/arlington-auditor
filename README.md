# p25-trunk-logger

control channel logger for P25 trunked radio systems. pulls system details, site frequencies, and talkgroups from RadioReference SOAP API, generates trunk-recorder config.json + talkgroups.csv, runs logging frontend.

## pipeline

1. **rr_config_gen.py** — connect to RadioReference with OAuth credentials, fetch system metadata (WACN, SYSID, all sites, all talkgroups)
2. generate trunk-recorder config (frequencies, talkgroup definitions, SDR parameters)
3. **trunk-recorder** — monitor each control channel, log transmissions, decode PI headers
4. optional: post-process logs for forensics/compliance

## usage

```bash
# Build config from RadioReference
python rr_config_gen.py \
    --system-id 10471 \
    --api-key YOUR_RR_KEY \
    --username YOUR_RR_USER \
    --password YOUR_RR_PASS \
    --sdr-device "rtl=0" \
    --sdr-rate 2048000 \
    --sdr-gain 38 \
    --output-dir ./p25-logger

# Then run trunk-recorder (built separately)
cd p25-logger && trunk-recorder
```

## radioReference credentials

RadioReference requires:
- system ID (e.g., 10471 for a public safety system in your region)
- API key (apply at radioreference.com)
- username / password (RadioReference account)

output: `config.json` and `talkgroups.csv` populated with live system geometry.

## notes

intended for P25 Phase I/II systems. EDACS and conventional FM systems require different decoders.
