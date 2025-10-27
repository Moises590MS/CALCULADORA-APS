import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class APScalculadora extends JFrame implements ActionListener {
    private JTextField display;
    private JTextArea historico;
    private String operador = "";
    private double primeiroNumero = 0;
    private boolean novoNumero = true;
    private int contDivisoes = 0; // contador de divisões para número primo

    public APScalculadora() {
        setTitle("Calculadora");
        setSize(450, 500);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout(5,5));

        display = new JTextField("0");
        display.setHorizontalAlignment(JTextField.RIGHT);
        display.setEditable(false);
        display.setFont(new Font("Arial", Font.BOLD, 28));
        add(display, BorderLayout.NORTH);

        historico = new JTextArea();
        historico.setEditable(false);
        historico.setFont(new Font("Arial", Font.PLAIN, 14));
        JScrollPane scroll = new JScrollPane(historico);
        scroll.setPreferredSize(new Dimension(150,100));
        add(scroll, BorderLayout.EAST);

        JPanel painelBotoes = new JPanel();
        painelBotoes.setLayout(new GridLayout(6, 5, 5, 5));

        String[] botoes = {
            "C","H", "√", "x²",
            "1/x","%","+","-",
            "7", "8", "9", "/",
            "4", "5", "6","*",
            "1", "2", "3","=",
             ".","0"
            
        };

        for (String texto : botoes) {
            JButton botao = new JButton(texto);
            botao.setFont(new Font("Arial", Font.BOLD, 18));
            botao.addActionListener(this);
            painelBotoes.add(botao);
        }

        add(painelBotoes, BorderLayout.CENTER);
        setVisible(true);
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        String comando = e.getActionCommand();
         
        if (comando.equals("H")) { // Limpar histórico
         historico.setText("");
}

        if (comando.matches("[0-9]")) {
            if (novoNumero) {
                display.setText(comando);
                novoNumero = false;
            } else {
                display.setText(display.getText() + comando);
            }
        } else if (comando.equals(".")) {
            if (!display.getText().contains(".")) {
                display.setText(display.getText() + ".");
                novoNumero = false;
            }
        } else if (comando.equals("C")) {
            display.setText("0");
            operador = "";
            primeiroNumero = 0;
            novoNumero = true;
            contDivisoes = 0;
        } else if (comando.equals("=")) {
            calcular(Double.parseDouble(display.getText()));
            operador = "";
        } else if (comando.equals("√")) {
            double valor = Double.parseDouble(display.getText());
            if (valor >= 0) {
                valor = Math.sqrt(valor);
                atualizarDisplay(valor, "√");
            } else {
                JOptionPane.showMessageDialog(this, "Erro: Raiz de número negativo!");
            }
        } else if (comando.equals("x²")) {
            double valor = Double.parseDouble(display.getText());
            valor = valor * valor;
            atualizarDisplay(valor, "^2");
        } else if (comando.equals("1/x")) {
            double valor = Double.parseDouble(display.getText());
            if (valor != 0) {
                valor = 1 / valor;
                atualizarDisplay(valor, "1/");
            } else {
                JOptionPane.showMessageDialog(this, "Erro: Divisão por zero!");
            }
        } else if (comando.equals("%")) {
            double valor = Double.parseDouble(display.getText());
            valor = primeiroNumero * valor / 100;
            atualizarDisplay(valor, "%");
        } else { // operadores +, -, *, /
            if (!operador.isEmpty()) {
                calcular(Double.parseDouble(display.getText()));
            } else {
                primeiroNumero = Double.parseDouble(display.getText());
            }
            operador = comando;
            novoNumero = true;
        }
    }

    private void calcular(double segundoNumero) {
        double resultado = 0;

        // Checar regra de números primos para divisões
        if (operador.equals("/") && ehInteiro(primeiroNumero) && ehPrimo((int) primeiroNumero)) {
            contDivisoes++;
            if (contDivisoes > 2) {
                JOptionPane.showMessageDialog(this, "Erro: Número primo não pode ser dividido mais de 2 vezes!");
                display.setText(String.valueOf((int) primeiroNumero));
                contDivisoes = 2; // manter contador no máximo
                return;
            }
        }

        switch (operador) {
            case "+": resultado = primeiroNumero + segundoNumero; break;
            case "-": resultado = primeiroNumero - segundoNumero; break;
            case "*": resultado = primeiroNumero * segundoNumero; break;
            case "/": 
                if (segundoNumero != 0) {
                    resultado = primeiroNumero / segundoNumero; 
                } else {
                    JOptionPane.showMessageDialog(this, "Erro: Divisão por zero!");
                    display.setText("0");
                    operador = "";
                    novoNumero = true;
                    return;
                }
                break;
            default: resultado = segundoNumero;
        }

        atualizarDisplay(resultado, operador);
        primeiroNumero = resultado;
        novoNumero = true;
    }

    private void atualizarDisplay(double valor, String operacao) {
        String texto;
        if (valor == (int) valor) {
            texto = String.valueOf((int) valor);
        } else {
            texto = String.valueOf(valor);
        }
        historico.append(operacao + " " + display.getText() + " = " + texto + "\n");
        display.setText(texto);
    }

    private boolean ehInteiro(double num) {
        return num == (int) num;
    }

    private boolean ehPrimo(int num) {
        if (num <= 1) return false;
        for (int i = 2; i <= Math.sqrt(num); i++) {
            if (num % i == 0) return false;
        }
        return true;
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> new APScalculadora());
    }
}
