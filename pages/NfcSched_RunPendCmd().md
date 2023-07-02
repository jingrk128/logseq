- NOW 為什麼只做到opCmd->state = NFC_SCHED_WAIT_RESULT？
  :LOGBOOK:
  CLOCK: [2023-05-21 Sun 18:29:46]
  CLOCK: [2023-05-21 Sun 18:29:49]
  :END:
  不做到NFC_SCHED_DONE嗎？
  如果是從NFCInf_Sync_Req()執行進來的，不就等於只做到一半就停止了？