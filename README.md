function cinematica_directa_gui

    % =========================================================
    % CINEMATICA DIRECTA - ROBOT 4 GDL
    % =========================================================

    % Crear ventana
    fig = uifigure( ...
        'Name','Cinemática Directa - Robot 4 GDL', ...
        'Position',[100 100 1100 650]);

    % ---------------------------------------------------------
    % TITULO
    % ---------------------------------------------------------

    uilabel(fig, ...
        'Text','CINEMÁTICA DIRECTA - ROBOT 4 GDL', ...
        'FontSize',20, ...
        'FontWeight','bold', ...
        'Position',[350 600 450 30]);

    % ---------------------------------------------------------
    % DATOS DEL ROBOT
    % ---------------------------------------------------------

    uilabel(fig, ...
        'Text','Longitudes', ...
        'FontSize',15, ...
        'FontWeight','bold', ...
        'Position',[30 545 150 25]);

    % l1
    uilabel(fig,'Text','l1:', ...
        'Position',[30 505 30 22]);

    l1Edit = uieditfield(fig,'numeric', ...
        'Position',[60 505 90 22], ...
        'Value',5);

    % l2
    uilabel(fig,'Text','l2:', ...
        'Position',[30 465 30 22]);

    l2Edit = uieditfield(fig,'numeric', ...
        'Position',[60 465 90 22], ...
        'Value',5);

    % l3
    uilabel(fig,'Text','l3:', ...
        'Position',[30 425 30 22]);

    l3Edit = uieditfield(fig,'numeric', ...
        'Position',[60 425 90 22], ...
        'Value',4);

    % l4
    uilabel(fig,'Text','l4:', ...
        'Position',[30 385 30 22]);

    l4Edit = uieditfield(fig,'numeric', ...
        'Position',[60 385 90 22], ...
        'Value',3);


    % ---------------------------------------------------------
    % ANGULOS
    % ---------------------------------------------------------

    uilabel(fig, ...
        'Text','Ángulos (grados)', ...
        'FontSize',15, ...
        'FontWeight','bold', ...
        'Position',[190 545 180 25]);

    % theta 1
    uilabel(fig,'Text','θ1:', ...
        'Position',[190 505 35 22]);

    t1Edit = uieditfield(fig,'numeric', ...
        'Position',[225 505 90 22], ...
        'Value',30);

    % theta 2
    uilabel(fig,'Text','θ2:', ...
        'Position',[190 465 35 22]);

    t2Edit = uieditfield(fig,'numeric', ...
        'Position',[225 465 90 22], ...
        'Value',30);

    % theta 3
    uilabel(fig,'Text','θ3:', ...
        'Position',[190 425 35 22]);

    t3Edit = uieditfield(fig,'numeric', ...
        'Position',[225 425 90 22], ...
        'Value',20);

    % theta 4
    uilabel(fig,'Text','θ4:', ...
        'Position',[190 385 35 22]);

    t4Edit = uieditfield(fig,'numeric', ...
        'Position',[225 385 90 22], ...
        'Value',10);


    % ---------------------------------------------------------
    % SELECCION DE METODO
    % ---------------------------------------------------------

    uilabel(fig, ...
        'Text','Método de solución', ...
        'FontSize',15, ...
        'FontWeight','bold', ...
        'Position',[30 335 180 25]);

    geometricaCheck = uicheckbox(fig, ...
        'Text','Solución geométrica', ...
        'Position',[30 300 180 25], ...
        'Value',true);

    matricesCheck = uicheckbox(fig, ...
        'Text','Solución con matrices', ...
        'Position',[30 265 190 25], ...
        'Value',false);


    % ---------------------------------------------------------
    % BOTON CALCULAR
    % ---------------------------------------------------------

    uibutton(fig, ...
        'push', ...
        'Text','CALCULAR', ...
        'FontSize',14, ...
        'FontWeight','bold', ...
        'Position',[80 210 180 45], ...
        'ButtonPushedFcn',@calcular);


    % ---------------------------------------------------------
    % RESULTADOS
    % ---------------------------------------------------------

    uilabel(fig, ...
        'Text','Resultados', ...
        'FontSize',15, ...
        'FontWeight','bold', ...
        'Position',[350 545 150 25]);

    resultado = uitextarea(fig, ...
        'Position',[350 330 330 205], ...
        'Editable','off', ...
        'FontSize',13);

    % ---------------------------------------------------------
    % MATRIZ
    % ---------------------------------------------------------

    uilabel(fig, ...
        'Text','Matriz homogénea', ...
        'FontSize',15, ...
        'FontWeight','bold', ...
        'Position',[350 295 200 25]);

    matrizTexto = uitextarea(fig, ...
        'Position',[350 100 330 190], ...
        'Editable','off', ...
        'FontSize',12);


    % ---------------------------------------------------------
    % GRAFICA 3D
    % ---------------------------------------------------------

    ax = uiaxes(fig, ...
        'Position',[700 100 370 435]);

    title(ax,'Configuración del robot')
    xlabel(ax,'X')
    ylabel(ax,'Y')
    zlabel(ax,'Z')

    grid(ax,'on')
    view(ax,3)


    % =========================================================
    % FUNCION PRINCIPAL DE CALCULO
    % =========================================================

    function calcular(~,~)

        % -----------------------------------------------------
        % Leer datos
        % -----------------------------------------------------

        l1 = l1Edit.Value;
        l2 = l2Edit.Value;
        l3 = l3Edit.Value;
        l4 = l4Edit.Value;

        th1 = t1Edit.Value;
        th2 = t2Edit.Value;
        th3 = t3Edit.Value;
        th4 = t4Edit.Value;


        % -----------------------------------------------------
        % Comprobar seleccion
        % -----------------------------------------------------

        if geometricaCheck.Value == matricesCheck.Value

            uialert(fig, ...
                'Selecciona solamente un método de solución.', ...
                'Error');

            return
        end


        % -----------------------------------------------------
        % SOLUCION GEOMETRICA
        % -----------------------------------------------------

        [Ex, Ey, Ez] = cinematicaGeometrica( ...
            l1,l2,l3,l4,th1,th2,th3,th4);


        % -----------------------------------------------------
        % SOLUCION POR MATRICES
        % -----------------------------------------------------

        T = cinematicaMatrices( ...
            l1,l2,l3,l4,th1,th2,th3,th4);


        % -----------------------------------------------------
        % MOSTRAR RESULTADOS
        % -----------------------------------------------------

        texto = sprintf([ ...
            'Posición del efector final\n\n' ...
            'Ex = %.4f\n' ...
            'Ey = %.4f\n' ...
            'Ez = %.4f\n\n' ...
            'Unidades: mismas que las longitudes'], ...
            Ex,Ey,Ez);

        resultado.Value = splitlines(string(texto));


        % -----------------------------------------------------
        % MOSTRAR MATRIZ
        % -----------------------------------------------------

        matrizTexto.Value = { ...
            sprintf('[ %8.4f  %8.4f  %8.4f  %8.4f ]',T(1,:)), ...
            sprintf('[ %8.4f  %8.4f  %8.4f  %8.4f ]',T(2,:)), ...
            sprintf('[ %8.4f  %8.4f  %8.4f  %8.4f ]',T(3,:)), ...
            sprintf('[ %8.4f  %8.4f  %8.4f  %8.4f ]',T(4,:)) ...
            };


        % -----------------------------------------------------
        % DIBUJAR ROBOT
        % -----------------------------------------------------

        dibujarRobot(ax, ...
            l1,l2,l3,l4, ...
            th1,th2,th3,th4);

    end


    % =========================================================
    % FUNCION CINEMATICA GEOMETRICA
    % =========================================================

    function [Ex,Ey,Ez] = cinematicaGeometrica( ...
            l1,l2,l3,l4,th1,th2,th3,th4)

        % Alcance horizontal
        r = l2*cosd(th2) ...
            + l3*cosd(th2+th3) ...
            + l4*cosd(th2+th3+th4);

        % Posicion X
        Ex = r*cosd(th1);

        % Posicion Y
        Ey = r*sind(th1);

        % Posicion Z
        Ez = l1 ...
            + l2*sind(th2) ...
            + l3*sind(th2+th3) ...
            + l4*sind(th2+th3+th4);

    end


    % =========================================================
    % FUNCION CINEMATICA POR MATRICES
    % =========================================================

    function T = cinematicaMatrices( ...
            l1,l2,l3,l4,th1,th2,th3,th4)

        % -----------------------------------------------------
        % Matriz de rotacion Z
        % -----------------------------------------------------

        Rz = [ ...
            cosd(th1)  -sind(th1)  0  0;
            sind(th1)   cosd(th1)  0  0;
            0           0          1  0;
            0           0          0  1];


        % -----------------------------------------------------
        % Traslacion vertical l1
        % -----------------------------------------------------

        Tz = [ ...
            1  0  0  0;
            0  1  0  0;
            0  0  1  l1;
            0  0  0  1];


        % -----------------------------------------------------
        % Rotacion Y(-theta2)
        % -----------------------------------------------------

        Ry2 = [ ...
            cosd(th2)   0  -sind(th2)  0;
            0           1   0          0;
            sind(th2)   0   cosd(th2)  0;
            0           0   0          1];


        % Traslacion l2
        Tx2 = [ ...
            1  0  0  l2;
            0  1  0  0;
            0  0  1  0;
            0  0  0  1];


        % -----------------------------------------------------
        % Rotacion Y(-theta3)
        % -----------------------------------------------------

        Ry3 = [ ...
            cosd(th3)   0  -sind(th3)  0;
            0           1   0          0;
            sind(th3)   0   cosd(th3)  0;
            0           0   0          1];


        % Traslacion l3
        Tx3 = [ ...
            1  0  0  l3;
            0  1  0  0;
            0  0  1  0;
            0  0  0  1];


        % -----------------------------------------------------
        % Rotacion Y(-theta4)
        % -----------------------------------------------------

        Ry4 = [ ...
            cosd(th4)   0  -sind(th4)  0;
            0           1   0          0;
            sind(th4)   0   cosd(th4)  0;
            0           0   0          1];


        % Traslacion l4
        Tx4 = [ ...
            1  0  0  l4;
            0  1  0  0;
            0  0  1  0;
            0  0  0  1];


        % -----------------------------------------------------
        % PRODUCTO TOTAL
        % -----------------------------------------------------

        T = Rz * Tz * Ry2 * Tx2 * Ry3 * Tx3 * Ry4 * Tx4;

    end


    % =========================================================
    % DIBUJAR ROBOT
    % =========================================================

    function dibujarRobot(ax,l1,l2,l3,l4, ...
            th1,th2,th3,th4)

        % -----------------------------------------------------
        % Coordenadas de los puntos
        % -----------------------------------------------------

        % Base
        P0 = [0;0;0];


        % Articulacion 1
        P1 = [0;0;l1];


        % Primer eslabon
        r2 = l2*cosd(th2);

        P2 = [ ...
            r2*cosd(th1);
            r2*sind(th1);
            l1 + l2*sind(th2)];


        % Segundo eslabon
        r3 = l3*cosd(th2+th3);

        P3 = [ ...
            (r2+r3)*cosd(th1);
            (r2+r3)*sind(th1);
            l1 ...
            + l2*sind(th2) ...
            + l3*sind(th2+th3)];


        % Tercer eslabon
        r4 = l4*cosd(th2+th3+th4);

        P4 = [ ...
            (r2+r3+r4)*cosd(th1);
            (r2+r3+r4)*sind(th1);
            l1 ...
            + l2*sind(th2) ...
            + l3*sind(th2+th3) ...
            + l4*sind(th2+th3+th4)];


        % -----------------------------------------------------
        % Dibujar
        % -----------------------------------------------------

        cla(ax)

        hold(ax,'on')

        % Ejes de referencia
        plot3(ax,[0 0],[0 0],[0 l1], ...
            'k--','LineWidth',1);

        % Robot
        plot3(ax, ...
            [P0(1) P1(1) P2(1) P3(1) P4(1)], ...
            [P0(2) P1(2) P2(2) P3(2) P4(2)], ...
            [P0(3) P1(3) P2(3) P3(3) P4(3)], ...
            '-o', ...
            'LineWidth',3, ...
            'MarkerSize',7);


        % Etiquetas
        text(ax,P0(1),P0(2),P0(3),'  Base')
        text(ax,P1(1),P1(2),P1(3),'  J1')
        text(ax,P2(1),P2(2),P2(3),'  J2')
        text(ax,P3(1),P3(2),P3(3),'  J3')
        text(ax,P4(1),P4(2),P4(3),'  Efector')


        % Configuracion de grafica
        grid(ax,'on')
        axis(ax,'equal')
        view(ax,3)

        xlabel(ax,'X')
        ylabel(ax,'Y')
        zlabel(ax,'Z')

        title(ax,'Robot - Configuración actual')

        hold(ax,'off')

    end

end
