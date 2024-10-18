const t8042 = document.querySelector(
  "[style*='margin-left: -12px; margin-top: -41px; width: 25px; height: 25px; transform: translate3d(562px, 203px, 0px)']"
);
const item = t8042;
const runFullLoop = true;
const baseTimeout = 15000; //24000;
const ymd = ["2024-10-22"];
const mode = "exam"; //'exam' || 'registration'
const plateNum = "_";

const carType = "tsc"; //'own' | 'tsc'
const kpp = "auto"; //'mech' | 'auto'
const category = "B"; //'B' | 'A'

// #region
const bookButton = document.querySelector(
  `[onclick="location.href='/site/step'"]`
);
const practiceTestButton = document.querySelector("[href='/site/step_pe']");
const tscCar = document.querySelector(
  '[data-target="#ModalCenterServiceCenter"]'
);
const ownCarElement = document.querySelector('[data-target="#ModalCenter2"]');
const successTheory = document.querySelector('[data-target="#ModalCenter4"]');

const confirmTscCar = document.querySelector('[href="/site/step_cs"]');
const cancelBooking = document.querySelector('[href="/site/undocherga"]');
const tscBmech = document.querySelector("[data-params='{\"value\":55}']");
const tscBauto = document.querySelector("[data-params='{\"value\":58}']");

const confirmOwnCar = document.querySelector('[data-target="#ModalCenter5"]');

const ownBcat = document.querySelector("[data-params*='\"subid\":13']");
const ownAcat = document.querySelector("[data-params*='\"subid\":12']");

const tscAcat = document.querySelector("[data-params*='\"value\":60']");
const date = document.querySelector(`[data-params*="${ymd[0]}"]`);
//#endregion

const warningModal = document.querySelector("#id-findModal");
const reRegistration = document.querySelector('[href="/site/step_dp"]');
const noInspection = document.querySelector(
  '[data-params*=\'{"value":49,"subid":1}\']'
);

const capcha = document.querySelector("#captchaform");
const capchaButton = document.querySelector('[class="btn btn-warning"]');
const capchaDelay = 25000; // default 15000
const delayedReload = (delay = 4000) => {
  setTimeout(() => {
    location.reload();
  }, delay);
};

if (capcha) {
  console.log("dealyed capcha", capchaDelay, capchaButton);
  delayedReload(capchaDelay + 4000);

  setTimeout(() => {
    capchaButton.click();
  }, capchaDelay);
} else if (
  mode === "exam" &&
  !cancelBooking &&
  (bookButton ||
    date ||
    practiceTestButton ||
    tscCar ||
    ownCarElement ||
    successTheory ||
    confirmTscCar ||
    tscBmech ||
    tscBauto ||
    tscAcat ||
    ownAcat ||
    confirmOwnCar ||
    ownBcat)
) {
  navigateToPractice();
} else {
  bookSlot();
}

function navigateToPractice() {
  if (!(carType || ymd[0])) return;

  const isOwnCar = carType === "own";
  if (bookButton) bookButton.click();

  if (practiceTestButton) practiceTestButton.click();

  if (ownCarElement) {
    if (carType === "own") ownCarElement.click();
    if (carType === "tsc") tscCar.click();
  }

  if (successTheory) {
    successTheory.click();

    if (confirmTscCar) confirmTscCar.click();
    if (confirmOwnCar) confirmOwnCar.click();
  }

  if (category === "B") {
    if (!isOwnCar) {
      if (kpp === "mech" && tscBmech) tscBmech.click();
      if (kpp === "auto" && tscBauto) tscBauto.click();
    }

    if (isOwnCar && ownBcat) ownBcat.click();
  }

  if (category === "A") {
    if (!isOwnCar && tscAcat) tscAcat.click();
    if (isOwnCar && ownAcat) ownAcat.click();
  }

  if (date) date.click();
}

function bookSlot() {
  const finishLink = document.querySelector("[href='/site/finish']");
  if (finishLink) {
    finishLink.click();
    console.log("Successfully booked slot");
    return;
  }
  console.log("tried finishLink, but not found");

  const cancelled = document.querySelector("[href='/site/cancelled']");
  const consent = document.querySelector(
    "[href='https://zakon.rada.gov.ua/laws/show/z1152-20#Text']"
  );
  if (cancelled || consent) {
    console.log("not a bookable page - return");
    return;
  }

  if (item) {
    item.click();
  } else {
    if (runFullLoop && !capcha) {
      delayedReload();
    }
  }

  if (runFullLoop) {
    setTimeout(() => {
      const plateInput = document.querySelector("#plate");
      if (plateInput) plateInput.value = plateNum;

      const submitButton = document.querySelector("#submit");
      if (submitButton) {
        submitButton.click();
      } else {
        if (!capcha) {
          const currentYmd = navigation.currentEntry.url.match(
            /chdate=(\d{4}-\d{2}-\d{2})/
          )[1];
          const nextYmdIndex = ymd.indexOf(currentYmd) + 1;
          const nextYmd =
            nextYmdIndex < ymd.length ? ymd[nextYmdIndex] : ymd[0];
          location.href = `https://eq.hsc.gov.ua/site/step2?chdate=${nextYmd}&question_id=58&id_es=`;

          return;
        }
      }
    }, baseTimeout);

    setTimeout(() => {
      const finishLink = document.querySelector("[href='/site/finish']");
      if (finishLink) {
        finishLink.click();
        console.log("Successfully booked slot");
        return;
      }
    }, 5000);
  }
}
