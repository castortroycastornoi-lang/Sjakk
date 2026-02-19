// Initialiser sjakkreglene og brettet
var game = new Chess();
var board = null;
var statusEl = $('#status');

// Funksjon som kjøres når du plukker opp en brikke
function onDragStart (source, piece, position, orientation) {
  // Ikke lov å flytte brikker hvis spillet er over
  if (game.game_over()) return false;

  // Ikke lov å flytte motstanderens brikker
  if ((game.turn() === 'w' && piece.search(/^b/) !== -1) ||
      (game.turn() === 'b' && piece.search(/^w/) !== -1)) {
    return false;
  }
}

// Funksjon som kjøres når du slipper en brikke
function onDrop (source, target) {
  // Prøv å gjøre trekket
  var move = game.move({
    from: source,
    to: target,
    promotion: 'q' // Oppgraderer alltid til dronning for enkelhets skyld
  });

  // Hvis trekket er ulovlig, sprett brikken tilbake
  if (move === null) return 'snapback';

  updateStatus();
}

// Oppdaterer brettet etter at en brikke er flyttet
function onSnapEnd () {
  board.position(game.fen());
}

// Oppdater tekststatusen (Hvem sin tur, sjakkmatt, osv.)
function updateStatus () {
  var status = '';

  var moveColor = 'Hvit';
  if (game.turn() === 'b') {
    moveColor = 'Svart';
  }

  if (game.in_checkmate()) {
    status = 'Spillet er over, ' + moveColor + ' er i sjakkmatt.';
  } else if (game.in_draw()) {
    status = 'Spillet er over, det er remis (uavgjort).';
  } else {
    status = moveColor + ' sin tur';
    if (game.in_check()) {
      status += ', ' + moveColor + ' er i sjakk!';
    }
  }
  statusEl.html(status);
}

// Konfigurasjon for brettet
var config = {
  draggable: true,
  position: 'start',
  onDragStart: onDragStart,
  onDrop: onDrop,
  onSnapEnd: onSnapEnd
};

// Start brettet
board = Chessboard('board', config);
updateStatus();
