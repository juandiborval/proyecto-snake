# proyecto-snake
import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.awt.image.BufferedImage;
import java.io.File;
import java.io.IOException;
import javax.imageio.ImageIO;
import java.util.ArrayList;
import java.util.Random;

public class JuegoSerpiente extends JPanel implements ActionListener {

    private final int AnchoVentana = 700;
    private final int largoVentana = 700; 
    private final int TamañoSnake = 15;
    private final int TamañoSnake2 = 15; 
    private final int RetrasoI = 100;
    private final int Decremento = 10; 
    private final int Incremento= 5 ;
    private ArrayList<Point> snake;
    private ArrayList<Point> snake2;
    private ArrayList<Point> obstáculos;

    private Point Manzana; 
    private Point ManzanaTocha;

    private char Movimiento;
    private char Movimiento2;

    private boolean gameOver;
    private boolean UnJugador;
    private Timer Timer; 
    private Timer SegundoTimer; 
    private int puntaje;
    private int puntaje2; 
    private int tiempo;

    private BufferedImage fondo;
    private BufferedImage serpiente1;
    private BufferedImage serpiente2;
    private BufferedImage manzana;
    private BufferedImage manzanatocha;
    private BufferedImage obstaculo;

    public JuegoSerpiente() {

        setPreferredSize(new Dimension(AnchoVentana, largoVentana));
        setFocusable(true);

        agregarKeyListeners();
        SeleccionModo();
        initGame();

        try {
            fondo = ImageIO.read(new File("FONDOOO.png")); 
            manzana = ImageIO.read(new File("APPLLE.png"));
            manzanatocha = ImageIO.read(new File("MANZANA MORADA.png")); 
            serpiente1 = ImageIO.read(new File("SEEEP.png")); 
            serpiente2 = ImageIO.read(new File("SEPP02.png")); 
            obstaculo = ImageIO.read(new File("obstaculos.png"));


        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private void SeleccionModo() {
        String[] Opciones = {"Un Jugador", "Dos Jugadores"};
        int Eleccion = JOptionPane.showOptionDialog(this, "Selecciona el modo de juego", "Modo de Juego",
                JOptionPane.DEFAULT_OPTION, JOptionPane.INFORMATION_MESSAGE, null, Opciones, Opciones[0]);
        
            if (Eleccion == JOptionPane.CLOSED_OPTION) {
                System.exit(0); }
        
            UnJugador = (Eleccion == 0);
    }
    
    private void agregarKeyListeners() {
        addKeyListener(new KeyAdapter() {
            public void keyPressed(KeyEvent e) {
                switch (e.getKeyCode()) {
                    case KeyEvent.VK_W:
                        if (Movimiento != 'S') Movimiento = 'W';
                        break;
                    case KeyEvent.VK_S:
                        if (Movimiento != 'W') Movimiento = 'S';
                        break;
                    case KeyEvent.VK_A:
                        if (Movimiento != 'D') Movimiento = 'A';
                        break;
                    case KeyEvent.VK_D:
                        if (Movimiento != 'A') Movimiento = 'D';
                        break;
                }

                if (!UnJugador) {
                    switch (e.getKeyCode()) {
                        case KeyEvent.VK_UP:
                            if (Movimiento2 != 'S') Movimiento2 = 'W';
                            break;
                        case KeyEvent.VK_DOWN:
                            if (Movimiento2 != 'W') Movimiento2 = 'S';
                            break;
                        case KeyEvent.VK_LEFT:
                            if (Movimiento2 != 'D') Movimiento2 = 'A';
                            break;
                        case KeyEvent.VK_RIGHT:
                            if (Movimiento2 != 'A') Movimiento2 = 'D';
                            break;
                    }
                }
            }
        });
    }

    private void initGame() {
        snake = new ArrayList<>();
        snake.add(new Point(35, 20));
        Movimiento = 'i';

        obstáculos = new ArrayList<>();
        generarObstáculos();

        if (!UnJugador) {
            snake2 = new ArrayList<>();
            snake2.add(new Point(25, 30));
            Movimiento2 = 'L';

            obstáculos = new ArrayList<>();
            generarObstáculos();
        }

        SpawnManzana();
        ManzanaTocha();
        gameOver = false;
        puntaje = 0;
        puntaje2 = 0; 
        tiempo = 0; 
        Timer = new Timer(RetrasoI, this);
        Timer.start();

        SegundoTimer = new Timer(1000, new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                if (!gameOver) {
                    tiempo++;
                    repaint(); 
                }
            }
        });
        SegundoTimer.start();
    }

    private void generarObstáculos() {
        Random rand = new Random();
        for (int i = 0; i < 25; i++) {

            int xMin = 20 / TamañoSnake;
            int yMin = 20 / TamañoSnake;
            int xMax = (AnchoVentana / TamañoSnake) - (20 / TamañoSnake) - 1;
            int yMax = (largoVentana / TamañoSnake) - (20 / TamañoSnake) - 1;

            Point nuevoObstaculo;
            boolean posicionvalida;

            do {
                int x = rand.nextInt(xMax - xMin + 1) + xMin;
                int y = rand.nextInt(yMax - yMin + 1) + yMin;
                nuevoObstaculo = new Point(x, y);
                posicionvalida = !obstáculos.contains(nuevoObstaculo) &&
                                  !snake.contains(nuevoObstaculo) &&
                                  (snake2 == null || !snake2.contains(nuevoObstaculo)) &&
                                  !nuevoObstaculo.equals(Manzana) &&
                                  !nuevoObstaculo.equals(ManzanaTocha);
            } while (!posicionvalida);

            obstáculos.add(nuevoObstaculo);
        }
    }
            
        
    

    private void cambiarObstáculos() {
        obstáculos.clear();
        generarObstáculos();
    }

    private void SpawnManzana() {
        Random rand = new Random();
        int xMin = 20 / TamañoSnake;
        int yMin = 20 / TamañoSnake;
        int xMax = (AnchoVentana / TamañoSnake) - (20 / TamañoSnake) - 1;
        int yMax = (largoVentana / TamañoSnake) - (20 / TamañoSnake) - 1;
        int x = rand.nextInt(xMax - xMin + 1) + xMin;
        int y = rand.nextInt(yMax - yMin + 1) + yMin;
        Manzana = new Point(x, y);
    }

    private void ManzanaTocha() {
        Random rand = new Random();
        int xMin = 20 / TamañoSnake;
        int yMin = 20 / TamañoSnake;
        int xMax = (AnchoVentana / TamañoSnake) - (20 / TamañoSnake) - 1;
        int yMax = (largoVentana / TamañoSnake) - (20 / TamañoSnake) - 1;
        int x = rand.nextInt(xMax - xMin + 1) + xMin;
        int y = rand.nextInt(yMax - yMin + 1) + yMin;
        ManzanaTocha = new Point(x, y);
    }
    
    protected void paintComponent(Graphics g) {
        super.paintComponent(g);

        if (fondo != null) {
            g.drawImage(fondo, 0, 0, AnchoVentana, largoVentana, null);
        }

        if (gameOver) {
            MenuGameOver();
        } else {

            g.setColor(Color.BLACK);
            g.drawRect(20, 20, AnchoVentana - 40, largoVentana - 40);
            
            if (serpiente1 != null && !snake.isEmpty()) {
            Point cabeza = snake.get(0);
            g.drawImage(serpiente1, cabeza.x * TamañoSnake, cabeza.y * TamañoSnake, TamañoSnake, TamañoSnake, null);
            }

           Color colorAguamarina = new Color(1, 179, 163); 
           g.setColor(colorAguamarina);
           for (int i = 1; i < snake.size(); i++) { 
           Point p = snake.get(i);
           g.fillRect(p.x * TamañoSnake, p.y * TamañoSnake, TamañoSnake, TamañoSnake);
           }            

           if (!UnJugador && serpiente2 != null && !snake2.isEmpty()) {
            Point cabeza2 = snake2.get(0);
            g.drawImage(serpiente2, cabeza2.x * TamañoSnake, cabeza2.y * TamañoSnake, TamañoSnake, TamañoSnake, null);
           }

          if (!UnJugador) {
            Color colorNaranja = new Color(252, 152, 2);
            g.setColor(colorNaranja);
            for (int i = 1; i < snake2.size(); i++) { 
                Point p = snake2.get(i);
                g.fillRect(p.x * TamañoSnake, p.y * TamañoSnake, TamañoSnake, TamañoSnake);
            }
          }
            
            if (obstaculo != null) {
            for (Point p : obstáculos) {
                g.drawImage(obstaculo, p.x * TamañoSnake, p.y * TamañoSnake, TamañoSnake, TamañoSnake, null);
            }
            }

            if (manzana != null) {
                g.drawImage(manzana, Manzana.x * TamañoSnake, Manzana.y * TamañoSnake, TamañoSnake, TamañoSnake, null);
            }

            if (manzanatocha != null) {
                g.drawImage(manzanatocha, ManzanaTocha.x * TamañoSnake, ManzanaTocha.y * TamañoSnake, TamañoSnake, TamañoSnake, null);
            }

            g.setColor(Color.BLACK);
            g.drawString("PUNTAJE: " + puntaje, 10, 10); 
            if (!UnJugador) {
                g.drawString("PUNTAJE2: " + puntaje2, 600, 10); 
            }
            g.drawString("TIEMPO: " + tiempo + "s", 300, 10); 
        }
    }

    public void actionPerformed(ActionEvent e) {
        if (!gameOver) {
            Mover();
            if (!UnJugador) {
                mover2();
            }
            checkCollision();
            if (!UnJugador) {
                checkCollision2();
            }
            checkCollisionObstáculos();
            repaint();
        } else {

            SegundoTimer.stop();
        }
    }
    
    private void Mover() {
        Point Cabeza = snake.get(0);
        Point CabezaNueva = new Point(Cabeza);

        switch (Movimiento) {
            case 'W': CabezaNueva.y--; break;
            case 'S': CabezaNueva.y++; break;
            case 'A': CabezaNueva.x--; break;
            case 'D': CabezaNueva.x++; break;
        }

        if (CabezaNueva.equals(Manzana)) {
            snake.add(0, CabezaNueva);
            SpawnManzana();
            DileyManzana();
            puntaje+=10;
            if (puntaje == puntaje%100000 ) {
                cambiarObstáculos();
            }
        } else if (CabezaNueva.equals(ManzanaTocha)) {
            snake.add(0, CabezaNueva);
            ManzanaTocha();
            DileyManzanatocha();
            puntaje ++;
        } else {
            snake.add(0, CabezaNueva);
            snake.remove(snake.size() - 1);
        }
    }

    private void mover2() {
        Point Cabeza2 = snake2.get(0);
        Point CabezaNueva2 = new Point(Cabeza2);

        switch (Movimiento2) {
            case 'W': CabezaNueva2.y--; break;
            case 'S': CabezaNueva2.y++; break;
            case 'A': CabezaNueva2.x--; break;
            case 'D': CabezaNueva2.x++; break;
        }


        if (CabezaNueva2.equals(Manzana)) {
            snake2.add(0, CabezaNueva2);
            SpawnManzana();
            DileyManzana();
            puntaje2+=10;
            if (puntaje == puntaje%100000 ) {
                cambiarObstáculos();
            }

        }else if (CabezaNueva2.equals(ManzanaTocha)) {
            snake2.add(0, CabezaNueva2);
            ManzanaTocha();
            DileyManzanatocha();
            puntaje2 ++;
        }else {
            snake2.add(0, CabezaNueva2);
            snake2.remove(snake2.size() - 1);
        }
        
    }

    private void DileyManzanatocha() {
        int delay = Timer.getDelay();
        if (delay > Decremento) {
            Timer.setDelay(delay - Decremento);
        }
    }

    private void DileyManzana() {
        int delay = Timer.getDelay();
        if (delay > Incremento) {
            Timer.setDelay(delay + Decremento);
        }
    }

    private void checkCollision() {
        Point head = snake.get(0);
        int xMin = 20 / TamañoSnake;
        int yMin = 20 / TamañoSnake;
        int xMax = (AnchoVentana / TamañoSnake) - (20 / TamañoSnake) - 1;
        int yMax = (largoVentana / TamañoSnake) - (20 / TamañoSnake) - 1;
    
        if (head.x < xMin || head.x > xMax || head.y < yMin || head.y > yMax || snake.subList(1, snake.size()).contains(head) || checkObstáculoColicion(head)) {
            gameOver = true;
        }
    }
    

    private void checkCollision2() {
        Point head2 = snake2.get(0);
        int xMin = 20 / TamañoSnake;
        int yMin = 20 / TamañoSnake;
        int xMax = (AnchoVentana / TamañoSnake) - (20 / TamañoSnake) - 1;
        int yMax = (largoVentana / TamañoSnake) - (20 / TamañoSnake) - 1;
        if (head2.x < xMin || head2.x > xMax || head2.y < yMin || head2.y > yMax || snake2.subList(1, snake2.size()).contains(head2) || checkObstáculoColicion(head2)) {
            gameOver = true;
        }
    }

    private void checkCollisionObstáculos() {
        if (checkObstáculoColicion(snake.get(0)) || (snake2 != null && checkObstáculoColicion(snake2.get(0)))) {
            gameOver = true;
        }
    }

    private boolean checkObstáculoColicion(Point cabeza) {
        for (Point obstáculo : obstáculos) {
            if (cabeza.equals(obstáculo)) {
                return true;
            }
        }
        return false;
    }

    private void MenuGameOver() {
        String mensaje = "Game Over\n";
        mensaje += "Puntaje: " + puntaje; 
        if (!UnJugador) {
            mensaje += "\nPuntaje 2: " + puntaje2; 
        }
        mensaje += "\n¿Quieres volver a intentar o salir?";
    
        int Opcion = JOptionPane.showOptionDialog(this,
                mensaje,
                "Game Over",
                JOptionPane.YES_NO_OPTION,
                JOptionPane.QUESTION_MESSAGE,
                null,
                new String[]{"Volver a Intentar", "Salir"},
                "Volver a Intentar");
        
        if (Opcion == JOptionPane.YES_OPTION) {
            initGame();
        } else {
            System.exit(0);
        }
    }

    public static void main(String[] args) {
        JFrame Ventana = new JFrame("Serpiente Game");
        JuegoSerpiente game = new JuegoSerpiente();
        Ventana.add(game);
        Ventana.pack();
        Ventana.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        Ventana.setVisible(true);
        Ventana.setResizable(false);
    }
}
