# JogoSnake
Fala somente sobre jogos arcade
import java.awt.*;
import java.awt.event.*;
import java.util.Random;
import javax.swing.*;

public class JogoSnake extends JPanel implements ActionListener {

    // Configurações do Jogo
    private final int LARGURA_TELA = 400;
    private final int ALTURA_TELA = 400;
    private final int TAMANHO_BLOCO = 20;
    private final int UNIDADES = (LARGURA_TELA * ALTURA_TELA) / (TAMANHO_BLOCO * TAMANHO_BLOCO);
    
    // Coordenadas do corpo da cobra
    private final int x[] = new int[UNIDADES];
    private final int y[] = new int[UNIDADES];
    
    private int corpoCobra = 3; // Tamanho inicial
    private int macaX, macaY; // Posição da comida
    private char direcao = 'R'; // R: Right, L: Left, U: Up, D: Down
    private boolean rodando = false;
    private Timer timer;

    public JogoSnake() {
        this.setPreferredSize(new Dimension(LARGURA_TELA, ALTURA_TELA));
        this.setBackground(Color.BLACK);
        this.setFocusable(true);
        this.addKeyListener(new AdaptadorTeclado());
        iniciarJogo();
    }

    public void iniciarJogo() {
        criarMaca();
        rodando = true;
        timer = new Timer(150, this); // Velocidade do jogo (150ms)
        timer.start();
    }

    @Override
    public void paintComponent(Graphics g) {
        super.paintComponent(g);
        desenhar(g);
    }

    public void desenhar(Graphics g) {
        if (rodando) {
            // Desenha a Maçã
            g.setColor(Color.RED);
            g.fillOval(macaX, macaY, TAMANHO_BLOCO, TAMANHO_BLOCO);

            // Desenha a Cobra
            for (int i = 0; i < corpoCobra; i++) {
                if (i == 0) g.setColor(Color.GREEN); // Cabeça
                else g.setColor(new Color(45, 180, 0)); // Corpo
                g.fillRect(x[i], y[i], TAMANHO_BLOCO, TAMANHO_BLOCO);
            }
        } else {
            fimDeJogo(g);
        }
    }

    public void mover() {
        for (int i = corpoCobra; i > 0; i--) {
            x[i] = x[i - 1];
            y[i] = y[i - 1];
        }

        switch (direcao) {
            case 'U': y[0] -= TAMANHO_BLOCO; break;
            case 'D': y[0] += TAMANHO_BLOCO; break;
            case 'L': x[0] -= TAMANHO_BLOCO; break;
            case 'R': x[0] += TAMANHO_BLOCO; break;
        }
    }

    public void validarMaca() {
        if ((x[0] == macaX) && (y[0] == macaY)) {
            corpoCobra++;
            criarMaca();
        }
    }

    public void verificarColisao() {
        // Colisão com o próprio corpo
        for (int i = corpoCobra; i > 0; i--) {
            if ((x[0] == x[i]) && (y[0] == y[i])) rodando = false;
        }
        // Colisão com as bordas
        if (x[0] < 0 || x[0] >= LARGURA_TELA || y[0] < 0 || y[0] >= ALTURA_TELA) rodando = false;

        if (!rodando) timer.stop();
    }

    public void criarMaca() {
        macaX = new Random().nextInt((int) (LARGURA_TELA / TAMANHO_BLOCO)) * TAMANHO_BLOCO;
        macaY = new Random().nextInt((int) (ALTURA_TELA / TAMANHO_BLOCO)) * TAMANHO_BLOCO;
    }

    public void fimDeJogo(Graphics g) {
        g.setColor(Color.RED);
        g.setFont(new Font("Monospaced", Font.BOLD, 40));
        g.drawString("GAME OVER", 100, ALTURA_TELA / 2);
        g.setFont(new Font("Monospaced", Font.BOLD, 20));
        g.drawString("Pontos: " + (corpoCobra - 3), 150, (ALTURA_TELA / 2) + 50);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (rodando) {
            mover();
            validarMaca();
            verificarColisao();
        }
        repaint();
    }

    private class AdaptadorTeclado extends KeyAdapter {
        @Override
        public void keyPressed(KeyEvent e) {
            switch (e.getKeyCode()) {
                case KeyEvent.VK_LEFT: if (direcao != 'R') direcao = 'L'; break;
                case KeyEvent.VK_RIGHT: if (direcao != 'L') direcao = 'R'; break;
                case KeyEvent.VK_UP: if (direcao != 'D') direcao = 'U'; break;
                case KeyEvent.VK_DOWN: if (direcao != 'U') direcao = 'D'; break;
            }
        }
    }

    // Para testar o jogo individualmente:
    public static void main(String[] args) {
        JFrame frame = new JFrame("TDS Snake Game");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.add(new JogoSnake());
        frame.pack();
        frame.setLocationRelativeTo(null);
        frame.setVisible(true);
    }
}
